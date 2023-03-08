The code below shows an example of how to get JSON output from a Substrait plan created in R.

```
# remotes::install_github("voltrondata/substrait-r")
library(substrait, warn.conflicts = FALSE)
library(dplyr, warn.conflicts = FALSE)

compiler <- tibble::tibble(x = c(1, 2.3, 3.4, 4.5)) %>%
  arrow_substrait_compiler()

compiler$.fns[["round"]] <- function(x, digits = 0) {
  substrait_call(
    "rounding.round",
    x,
    digits,
    .output_type = substrait_fp64(),
    .options = list(
      substrait$FunctionOption$create(name = "rounding",preference = "TIE_TO_EVEN"))
    )
}

compiler2 <- compiler %>%
  substrait_project(y = round(x)) 

compiler2$plan() |> 
  as.raw() |> 
  arrow::buffer() |> 
  arrow:::substrait__internal__SubstraitToJSON() |> 
  jsonlite::prettify(indent = 2)
#> {
#>   "extensionUris": [
#>     {
#>       "extensionUriAnchor": 1,
#>       "uri": "https://github.com/substrait-io/substrait/blob/main/extensions/functions_arithmetic.yaml"
#>     },
#>     {
#>       "extensionUriAnchor": 2,
#>       "uri": "https://github.com/substrait-io/substrait/blob/main/extensions/functions_comparison.yaml"
#>     }
#>   ],
#>   "extensions": [
#>     {
#>       "extensionFunction": {
#>         "functionAnchor": 2,
#>         "name": "round"
#>       }
#>     }
#>   ],
#>   "relations": [
#>     {
#>       "root": {
#>         "input": {
#>           "project": {
#>             "common": {
#> 
#>             },
#>             "input": {
#>               "read": {
#>                 "baseSchema": {
#>                   "names": [
#>                     "x"
#>                   ],
#>                   "struct": {
#>                     "types": [
#>                       {
#>                         "fp64": {
#>                           "nullability": "NULLABILITY_NULLABLE"
#>                         }
#>                       }
#>                     ]
#>                   }
#>                 },
#>                 "namedTable": {
#>                   "names": [
#>                     "named_table_1"
#>                   ]
#>                 }
#>               }
#>             },
#>             "expressions": [
#>               {
#>                 "scalarFunction": {
#>                   "functionReference": 2,
#>                   "outputType": {
#>                     "fp64": {
#>                       "nullability": "NULLABILITY_NULLABLE"
#>                     }
#>                   },
#>                   "arguments": [
#>                     {
#>                       "value": {
#>                         "selection": {
#>                           "directReference": {
#>                             "structField": {
#> 
#>                             }
#>                           },
#>                           "rootReference": {
#> 
#>                           }
#>                         }
#>                       }
#>                     },
#>                     {
#>                       "value": {
#>                         "literal": {
#>                           "fp64": 0
#>                         }
#>                       }
#>                     }
#>                   ],
#>                   "options": [
#>                     {
#>                       "name": "rounding",
#>                       "preference": [
#>                         "TIE_TO_EVEN"
#>                       ]
#>                     }
#>                   ]
#>                 }
#>               }
#>             ]
#>           }
#>         },
#>         "names": [
#>           "x",
#>           "y"
#>         ]
#>       }
#>     }
#>   ]
#> }
#> 
```