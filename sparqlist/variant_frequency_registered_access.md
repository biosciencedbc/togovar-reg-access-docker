# Variant report / Frequency

## Parameters

* `tgv_id` TogoVar ID
  * default: tgv219804
* `access_token` Access Token

## Endpoint

{{SPARQLIST_TOGOVAR_SPARQL_REGISTERED_ACCESS}}


## `get_account_keycloak` Get account for API response

```javascript
async ({access_token, SPARQLIST_DDBJ_KEYCLOAK_URL})=>{
    const key_cloak_url = SPARQLIST_DDBJ_KEYCLOAK_URL +"/userinfo"
    const options = {
      method: 'POST',
      headers: {
        'Accept':'application/json',
        'Content-Type':'application/x-www-form-urlencoded;charset=UTF-8',
        'Authorization':'Bearer ' + access_token
      }
    };
    try{
      var res = await fetch(key_cloak_url, options).then(res=>res.json());
      return res.preferred_username;
    }catch(error){
      console.log(error);
    }
};
```

## `search_attribute` Search attribute for Virtuoso 

```sparql
prefix dct: <http://purl.org/dc/terms/>

SELECT ?period
FROM <http://togovar.biosciencedbc.jp/registered_access/ddbjaccount>
WHERE {
  VALUES ?account_id { "{{ get_account_keycloak }}"^^<http://www.w3.org/2001/XMLSchema#string> }
  ?account dct:identifier ?account_id;
    dct:valid ?period .
}
```

## `check_period` Check validity of period 

```javascript
({tgv_id, search_attribute}) => {
  if(search_attribute.results.bindings.length === 0){
     return "unregistered_user";
  }

  const period =search_attribute.results.bindings[0].period.value;
  if(!period || period === "" || period === "null"){
     return "unregistered_user";
  }

  const dt = new Date();
  const now_date = new Date(
     dt.getFullYear() + '-' + ('00' + (dt.getMonth()+1)).slice(-2) + '-' + ('00' + dt.getDate()).slice(-2)
  );
  const period_date = new Date(period);
  if(now_date.getTime() > period_date.getTime()){
     return "expired_user";
  }  
  
  return tgv_id;
}
```


## `search_frequency` Search Frequency for Virtuoso 

```sparql
DEFINE sql:select-option "order"

PREFIX dct: <http://purl.org/dc/terms/>
PREFIX sio: <http://semanticscience.org/resource/>
PREFIX tgvo: <http://togovar.biosciencedbc.jp/vocabulary/>
PREFIX exo: <http://purl.jp/bio/10/exac/>

SELECT ?source ?num_alleles ?num_alt_alleles ?frequency ?num_genotype_ref_homo ?num_genotype_hetero ?num_genotype_alt_homo (GROUP_CONCAT(DISTINCT ?_filter ; separator = ", ") AS ?filter) ?quality
FROM <http://togovar.biosciencedbc.jp/variation>
FROM <http://togovar.biosciencedbc.jp/variation/frequency/exac>
FROM <http://togovar.biosciencedbc.jp/variation/frequency/gem_j_wga>
FROM <http://togovar.biosciencedbc.jp/variation/frequency/hgvd>
FROM <http://togovar.biosciencedbc.jp/variation/frequency/jga_ngs>
FROM <http://togovar.biosciencedbc.jp/variation/frequency/jga_snp>
FROM <http://togovar.biosciencedbc.jp/variation/frequency/tommo_4.7kjpn>
WHERE {
  VALUES ?tgv_id { "{{check_period}}" }

  ?variation dct:identifier ?tgv_id ;
             rdfs:label ?label .

  {
    ?variation tgvo:statistics ?_stat .

    ?_stat dct:source ?source ;
        tgvo:alleleNumber ?num_alleles ;
        tgvo:alleleCount ?num_alt_alleles ;
        tgvo:alleleFrequency ?frequency .

    OPTIONAL { ?_stat tgvo:filter ?_filter . }
    OPTIONAL { ?_stat tgvo:quality ?_quality . }

    BIND (IF(?source IN ("ToMMo 4.7KJPN", "HGVD", "JGA-SNP"), undef, ?_quality) AS ?quality)

    OPTIONAL { ?_stat tgvo:homozygousReferenceAlleleCount ?num_genotype_ref_homo . }
    OPTIONAL { ?_stat tgvo:heterozygousAlleleCount ?num_genotype_hetero . }
    OPTIONAL { ?_stat tgvo:homozygousAlternativeAlleleCount ?num_genotype_alt_homo . }
  } UNION {
    ?exac dct:identifier ?label ;
          exo:alleleCount ?num_alt_alleles ;
          exo:alleleNum ?num_alleles ;
          exo:alleleFrequency ?frequency .

    OPTIONAL { ?exac exo:filter ?_filter . }
    OPTIONAL { ?exac tgvo:statistics/tgvo:quality ?quality . }

    BIND ("ExAC" AS ?source)
  } UNION {
    ?exac dct:identifier ?label ;
          exo:population ?_pop .

    ?_pop a exo:Population ;
            rdfs:label ?_population ;
            exo:alleleCount ?num_alt_alleles ;
            exo:alleleNum ?num_alleles ;
            exo:alleleFrequency ?frequency .

    BIND (CONCAT("ExAC:", ?_population) AS ?source)
  }
}
ORDER BY ?source
```
## `result` Compile results

```javascript
({get_account_keycloak, check_period, search_frequency}) =>{
   const message_no_response_account =
         "Failed to get account information";
   const message_not_authorized =
         "Not authorized, Please log in again with an authorized account";
   const message_expired =
         "The expiration date has passed, Please log in with an authorized account";

   if(!get_account_keycloak || get_account_keycloak === ""){
       search_frequency.results.bindings.push({
         error:  {
          "type": "literal",
          "value": message_no_response_account
         }
       });
   }

   if(check_period === "unregistered_user"){
       search_frequency.results.bindings.push({
         error:  {
          "type": "literal",
          "value": message_not_authorized
         }
       });
   }

   if(check_period === "expired_user"){
       search_frequency.results.bindings.push({
         error:  {
          "type": "literal",
          "value": message_expired
         }
       });
   }

   return search_frequency;
}
```
