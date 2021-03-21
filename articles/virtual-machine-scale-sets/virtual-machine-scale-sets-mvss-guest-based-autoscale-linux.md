---
title: Use a escala automática Azure com métricas de hóspedes em um modelo de conjunto de escala Linux
description: Saiba como fazer a escala automática usando métricas de hóspedes num modelo de conjunto de escala de máquina virtual Linux
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/26/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 0605780651e1a3c54ae53d13a3f99e1124fa76db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584998"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Autoescala usando métricas de hóspedes em um modelo de conjunto de escala Linux

Existem dois amplos tipos de métricas em Azure que são recolhidas a partir de VMs e conjuntos de escala: métricas de anfitrião e métricas de hóspedes. A um nível elevado, se você gostaria de usar CPU padrão, métricas de disco e rede, então as métricas do hospedeiro são um bom ajuste. Se, no entanto, precisar de uma maior seleção de métricas, então as métricas dos hóspedes devem ser analisadas.

As métricas do anfitrião não requerem configuração adicional porque são recolhidas pelo VM anfitrião, enquanto as métricas dos hóspedes exigem que você instale a [extensão de Diagnóstico do Windows Azure](../virtual-machines/extensions/diagnostics-template.md) ou a [extensão de Diagnóstico Linux Azure](../virtual-machines/extensions/diagnostics-linux.md) no VM convidado. Uma razão comum para usar métricas de hóspedes em vez de métricas de hospedeiro é que as métricas dos hóspedes fornecem uma maior seleção de métricas do que as métricas do hospedeiro. Um desses exemplos são as métricas de consumo de memória, que só estão disponíveis através de métricas de hóspedes. As métricas de anfitrião suportadas estão listadas [aqui,](../azure-monitor/essentials/metrics-supported.md)e as métricas de hóspedes geralmente usadas estão listadas [aqui.](../azure-monitor/autoscale/autoscale-common-metrics.md) Este artigo mostra como modificar o [modelo básico de conjunto de escala viável](virtual-machine-scale-sets-mvss-start.md) para usar regras de autoescala com base em métricas de hóspedes para conjuntos de escala Linux.

## <a name="change-the-template-definition"></a>Alterar a definição de modelo

Num [artigo anterior](virtual-machine-scale-sets-mvss-start.md) tínhamos criado um modelo de conjunto de escala básica. Vamos agora usar esse modelo anterior e modificá-lo para criar um modelo que implementa um conjunto de escala Linux com autoescala baseada em métricas de hóspedes.

Primeiro, adicione parâmetros para `storageAccountName` e `storageAccountSasToken` . O agente de diagnóstico armazena dados métricos numa [tabela](../cosmos-db/tutorial-develop-table-dotnet.md) nesta conta de armazenamento. A partir da versão 3.0 do Agente de Diagnóstico Linux, a utilização de uma chave de acesso ao armazenamento já não é suportada. Em vez disso, use um [Token SAS](../storage/common/storage-sas-overview.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Em seguida, modifique a escala definida `extensionProfile` para incluir a extensão de diagnóstico. Nesta configuração, especifique o ID de recurso da escala definida para recolher métricas, bem como a conta de armazenamento e o token SAS para usar para armazenar as métricas. Especificar com que frequência as métricas são agregadas (neste caso, a cada minuto) e quais as métricas a rastrear (neste caso, por cento de memória usada). Para obter informações mais detalhadas sobre esta configuração e métricas que não a porcento da memória usada, consulte [esta documentação](../virtual-machines/extensions/diagnostics-linux.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Finalmente, adicione um `autoscaleSettings` recurso para configurar a autoescala com base nestas métricas. Este recurso tem uma `dependsOn` cláusula que faz referência à escala definida para garantir que o conjunto de escala existe antes de tentar autodimensioná-la. Se escolher uma métrica diferente para a escala automática, utilizará a `counterSpecifier` configuração da extensão de diagnóstico como a `metricName` configuração de autoescala. Para obter mais informações sobre a configuração de autoescalação, consulte as [melhores práticas de autoescalação](../azure-monitor/autoscale/autoscale-best-practices.md) e a [documentação de referência da API do Monitor Azure](/rest/api/monitor/autoscalesettings).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
