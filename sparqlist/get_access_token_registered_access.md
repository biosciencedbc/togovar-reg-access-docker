# Access token / DDBJ

## Parameters

* `tgv_id` TogoVar ID
  * default: tgv83272260
* `code` Authorization code
  
## `search_api`
```javascript
async ({SPARQLIST_DDBJ_KEYCLOAK_CLIENT_ID, SPARQLIST_DDBJ_KEYCLOAK_CLIENT_SECRET, SPARQLIST_DDBJ_KEYCLOAK_URL, SPARQLIST_TOGOVAR_APP_HOST, code, tgv_id}) => {

  const redirect_uri = SPARQLIST_TOGOVAR_APP_HOST + "/variant/" + tgv_id
  const ddbj_keycloak = SPARQLIST_DDBJ_KEYCLOAK_URL + "/token"
  const params = 
        'client_id=' + encodeURIComponent(SPARQLIST_DDBJ_KEYCLOAK_CLIENT_ID)
        + '&client_secret=' + encodeURIComponent(SPARQLIST_DDBJ_KEYCLOAK_CLIENT_SECRET)
        + '&code=' + encodeURIComponent(code)
        + '&grant_type=' + encodeURIComponent('authorization_code')
        + '&redirect_uri=' + encodeURIComponent(redirect_uri);

  const options   = {
    method: 'POST',
    headers: {
      'Accept': 'application/json',
      'Content-Type':'application/x-www-form-urlencoded; charset=UTF-8'
    },
    body: params
  };

  try{
    const res = await fetch(ddbj_keycloak, options).then(res=>res.json());
    return res;
  }catch(error){
    console.log(error);
  }
}
```
