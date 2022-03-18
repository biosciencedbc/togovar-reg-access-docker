# Logout keycloak / DDBJ

## Parameters

* `refresh_token` Authorization code

  
## `search_api`
```javascript
async ({SPARQLIST_DDBJ_KEYCLOAK_CLIENT_ID, SPARQLIST_DDBJ_KEYCLOAK_CLIENT_SECRET, SPARQLIST_DDBJ_KEYCLOAK_URL, refresh_token}) => {

  const ddbj_keycloak = SPARQLIST_DDBJ_KEYCLOAK_URL + "/logout";
  const params = 
        'client_id=' + encodeURIComponent(SPARQLIST_DDBJ_KEYCLOAK_CLIENT_ID)
        + '&client_secret=' + encodeURIComponent(SPARQLIST_DDBJ_KEYCLOAK_CLIENT_SECRET)
        + '&refresh_token=' + encodeURIComponent(refresh_token);

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
