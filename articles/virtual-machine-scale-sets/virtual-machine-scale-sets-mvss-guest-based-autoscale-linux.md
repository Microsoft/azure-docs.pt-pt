---
title: Use a escala automática Azure com métricas de hóspedes num modelo de conjunto de escala Linux
description: Aprenda a escalar automaticamente usando métricas de hóspedes num modelo de conjunto de escala de máquina virtual Linux
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 8021b7b8feb6dc06fb2e48bc4e825200a1baad33
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273652"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Escala automática usando métricas de hóspedes em um modelo de conjunto de escala Linux

Existem dois tipos amplos de métricas em Azure que são recolhidas a partir de VMs e conjuntos de escala: métricas de hospedeiro e métricas de hóspedes. A um nível elevado, se quiser utilizar as métricas padrão de CPU, disco e rede, então as métricas de hospedeiro são um bom ajuste. Se, no entanto, precisar de uma maior seleção de métricas, então as métricas dos hóspedes devem ser examinadas.

As métricas do hospedeiro não requerem configuração adicional porque são recolhidas pelo VM anfitrião, enquanto as métricas dos hóspedes exigem que instale a extensão de [Diagnóstico sinuoso do Windows Azure](../virtual-machines/windows/extensions-diagnostics-template.md) ou a [extensão de Diagnóstico Sinuoso Linux Azure](../virtual-machines/linux/diagnostic-extension.md) no VM convidado. Uma razão comum para usar as métricas dos hóspedes em vez das métricas de hospedeiro é que as métricas dos hóspedes fornecem uma maior seleção de métricas do que as métricas de hospedeiro. Um desses exemplos são as métricas de consumo de memória, que só estão disponíveis através das métricas dos hóspedes. As métricas de anfitrião suportadas estão listadas [aqui,](../azure-monitor/platform/metrics-supported.md)e as métricas de hóspedes comumente usadas estão listadas [aqui.](../azure-monitor/platform/autoscale-common-metrics.md) Este artigo mostra como modificar o modelo básico de conjunto de [escala viável](virtual-machine-scale-sets-mvss-start.md) para usar regras de escala automática com base nas métricas dos hóspedes para conjuntos de escala Linux.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

Num [artigo anterior,](virtual-machine-scale-sets-mvss-start.md) tínhamos criado um modelo básico de conjunto de escala. Vamos agora usar esse modelo anterior e modificá-lo para criar um modelo que implementa um conjunto de escala Linux com escala automática baseada em métrica seletiva.

Primeiro, adicione parâmetros para `storageAccountName` e `storageAccountSasToken`. O agente de diagnóstico armazena dados métricos numa [tabela](../cosmos-db/table-storage-how-to-use-dotnet.md) nesta conta de armazenamento. A partir da versão 3.0 do Agente de Diagnósticos Linux, a utilização de uma chave de acesso ao armazenamento já não é suportada. Em vez disso, use um [Token SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

Em seguida, modifique a balança definida `extensionProfile` para incluir a extensão de diagnóstico. Nesta configuração, especifique o ID de recurso da escala definida para recolher métricas, bem como a conta de armazenamento e token SAS para usar para armazenar as métricas. Especifique com que frequência as métricas são agregadas (neste caso, a cada minuto) e quais as métricas a rastrear (neste caso, por cento de memória usada). Para obter informações mais detalhadas sobre esta configuração e métricas que não a de memória usada por cento, consulte [esta documentação](../virtual-machines/linux/diagnostic-extension.md).

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

Finalmente, adicione `autoscaleSettings` um recurso para configurar a escala automática com base nestas métricas. Este recurso `dependsOn` tem uma cláusula que faz referência à escala definida para garantir que o conjunto de escala existe antes de tentar autodimensioná-lo automaticamente. Se escolher uma métrica diferente para escalar automaticamente, utilizará a `counterSpecifier` configuração da extensão de diagnóstico como a `metricName` configuração de escala automática. Para obter mais informações sobre a configuração da escala automática, consulte as [melhores práticas](../azure-monitor/platform/autoscale-best-practices.md) de escala automática e a documentação de referência da [API do Monitor Azure.](/rest/api/monitor/autoscalesettings)

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
