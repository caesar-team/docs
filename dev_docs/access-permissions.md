# Access permissions

We are using the HATEOAS approach to get access to objects. All needed access permissions are implemented as links that describe which access a user has when the user opens an object. Based on the data, there’s a front-end interface that displays relevant action buttons/links.

This is a response example:

```text
"_links": {
    "team_members": {
        "href": "/api/teams/3b97a207-4150-472e-8c4d-a21d552b9ffc/members",
        "method": "GET"
    },
    "team_member_add": {
        "href": "/api/teams/3b97a207-4150-472e-8c4d-a21d552b9ffc/members/__USER__",
        "method": "POST"
    }
}
```

The BazingaHateoasBundle bundle is used to implement accesses, see [here](https://github.com/willdurand/BazingaHateoasBundle).

You can use **Annotation** or any other tool to declare all object permissions manually. Here’s an example of how to implement team permissions.

```text
/**
 * @Hateoas\Relation(
 *     "team_delete",
 *     attributes={"method": "DELETE"},
 *     href=@Hateoas\Route(
 *         "api_team_delete",
 *         parameters={ "team": "expr(object.id)" }
 *     ),
 *     exclusion=@Hateoas\Exclusion(
 *         excludeIf="expr(not is_granted(constant('App\\Security\\Voter\\TeamVoter::DELETE'), object.getTeam()))"
 *     )
 * )
 * @Hateoas\Relation(
 *     "team_member_add",
 *     attributes={"method": "POST"},
 *     href=@Hateoas\Route(
 *         "api_team_member_add",
 *         parameters={ "team": "expr(object.id)", "user": "__USER__" }
 *     ),
 *     exclusion=@Hateoas\Exclusion(
 *         excludeIf="expr(not is_granted(constant('App\\Security\\Voter\\UserTeamVoter::USER_TEAM_EDIT'), object.getTeam()))"
 *     )
 * )
 */
class TeamView {}
```

Access to a link should be implemented using a standard Symfony tool - [Voter ](https://symfony.com/doc/current/security/voters.html)or using ROLE\_\*.

Endpoints that do not require specific access permissions \(i.e. many GET endpoints require only ROLE\_USER\), so it’s not needed to apply them in permission.

Some specific permissions \(i.e. permissions to create a team\) can be implemented with this endpoint “_**/api/user/self**_ “, where each user can see their main permission in the app.

