# Auth2

This documentation is intended for developers who may undertake development of bespoke applications that will interface with the Auth2 single-sign-on (SSO) service.

## Overview

The Auth2 SSO service is a bespoke Django application which works in conjunction with the department's Nginx reverse proxy to allow web applications to use a variety of identity providers (e.g. Microsoft, Apple, Google, etc.) in order to authenticate users. It can also optionally be used to provide authorisation rules for users to access web application locations. Auth2 is supported and administered by OIM staff.

Simplified, the process for handling a HTTP request for an example integrated web application (`example.dbca.wa.gov.au`) is as follows:

1. The user's browser sends a HTTP request for `example.dbca.wa.gov.au` to Nginx.
1. Nginx receives the request and notes that the domain requires that users be signed into Auth2 to access. Nginx notes that the user is not yet signed in, and redirects the user to the Auth2 sign-in page.
1. The user signs into Auth, then is redirected to the originally-requested domain (`example.dbca.wa.gov.au`).
1. When processing subsequent requests for `example.dbca.wa.gov.au`, Nginx notes that the user is signed into Auth2, appends some additional HTTP headers onto the request identifying the user and forwards the request to the application backend.
1. The application backend receives the HTTP request with these identifying headers, logs the user in automatically, and returns a HTTP response.

## Application development

Integration with the Auth2 service by web applications is carried out via HTTP headers, which are appended to any HTTP request for domains that use the Auth2 SSO service.

Integrating with Auth2 does require some additional server-side development to make use of the additional HTTP headers which identify an authenticated user. OIM has developed an open source Django middleware class called `SSOLoginMiddleware` that processes all HTTP requests for a web application. It can be reviewed as an example for how integration may be carried out, or used as-is for Django projects: https://github.com/dbca-wa/dbca-utils/blob/master/dbca_utils/middleware.py

Additional HTTP headers which are appended to a request by Nginx for Auth2-authenticated users include the following:

- `X-Email` - the user's email address. This is the primary key for identifying an authenticated user.
- `X-First-name` - the user's first name.
- `X-Last-name` - the user's surname.
- `X-Groups` - a comma-separated list of the user's group memberships within Auth2 itself. This value can be optionally used for the purposes of authorisation.

**NOTE**: not all HTTP headers are guaranteed to be included with the HTTP response. For an authenticated user, the `X-Email` header will **always** be included. `X-First-name` and `X-Last-name` are not guaranteed be included, because an external identity provider might not always provide a value for those fields, therefore the values sent to Nginx will be empty. Likewise, it is possible for an authenticated user to not belong to any groups within Auth2, therefore the `X-Groups` header value might also be empty. Nginx will not forward HTTP headers with null values, so these headers will be absent from the response.

## Nginx rules summary

The requirement (or not) for SSO via Auth2 for web applications is managed by Nginx rules. These rules include several variations that allow different methods of authentication to be required. Rule can be applied at the domain level (i.e. the same rule will apply for all locations within a domain), or at the location level (i.e. different rules apply at each location). All rule types described below can be used at once within a domain for different locations.

| Nginx rule                        | Behaviour                                                                                                                                                                                                                   | Use cases                                       |
| --------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------- |
| SSO authentication required       | Upon request, non-authenticated users will be redirected to the Auth2 login page. Following authentication, users will be redirected back to the original location.                                                         | Web applications requiring user authentication. |
| Basic/SSO authentication required | Upon request, non-authenticated users will be presented with a HTTP basic authentication challenge to access the requested resource. Authenticated users (i.e. those with a current session cookie) will not be challenged. | REST API services, restricted-access resources. |
| No authentication required        | The requested resource will be served to the user (authenticated or otherwise).                                                                                                                                             | Publicly-accessible websites & resources.       |

### Example

The following examples illustrate how each of the available Nginx rules might be used for an example domain with differing authentication requirements at different locations.

| Domain path                   | Authentication required?                          | Explanation/rationale                                                                                                                                                                                                                                                                                                                                                                       |
| ----------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `www.example.wa.gov.au/`      | Not required                                      | Publicly-accessible website, therefore anonymous access is allowed. All HTTP requests may be stateless.                                                                                                                                                                                                                                                                                     |
| `www.example.wa.gov.au/admin` | Required                                          | Administrator functions are available, therefore a requests to this location require a user to be authenticated. There is no requirement for this location to be used by scripted/machine processes, therefore methods such as HTTP basic authentication are not supported. All HTTP requests are expected to be stateful (i.e. carried out using a browser with a current session cookie). |
| `www.example.wa.gov.au/api`   | Required (basic authentication method is allowed) | API endpoints are required to be available to both users and scripted processes, therefore HTTP basic authentication is supported as an option to allow stateless HTTP requests by scripted processes.                                                                                                                                                                                      |

## Authorisation

In addition to authentication, Auth2 can also be used to manage authorisation functions for integrated web applications via group membership within Auth2 itself. This can be carried out using the `X-Groups` header, which consists of a comma-separated list of the user's group memberships within Auth2 itself. This feature is optional, and is intended to be used by applications which do not have any existing authorisation mechanism.

### Example

| Authenticated user | X-Groups HTTP header value | Web application behaviour                                                                                                                 |
| ------------------ | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Adam               | PUBLIC                     | This user is a member of the `PUBLIC` group only, and is allowed only limited access to the application.                                  |
| Bob                | PUBLIC,DBCA                | This user is a member of the `PUBLIC` and `DBCA` groups, and is allowed additional access to the application functions.                   |
| Charlie            | PUBLC,DBCA,ADMINS          | This is a a member of the `PUBLIC`, `DBCA` and `ADMINS` groups, and is therefore allowed full access to all of the application functions. |
