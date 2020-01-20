---
title: Usar o dimensionamento automático do Azure com métricas de convidado em um modelo de conjunto de dimensionamento Linux
description: Saiba como usar o dimensionamento automático usando métricas de convidado em um modelo de conjunto de dimensionamento de máquinas virtuais do Linux
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 88f839b281e4d345b012a7e6acff3770dc536045
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271957"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Dimensionamento automático usando métricas de convidado em um modelo de conjunto de dimensionamento Linux

Há dois tipos amplos de métricas no Azure que são coletadas de VMs e conjuntos de dimensionamento: métricas de host e métricas de convidado. Em um alto nível, se você quiser usar as métricas de CPU, disco e rede padrão, as métricas de host serão uma boa opção. No entanto, se você precisar de uma seleção maior de métricas, as métricas de convidado devem ser examinadas.

As métricas do host não exigem configuração adicional porque são coletadas pela VM do host, enquanto as métricas de convidado exigem que você instale a [extensão do Windows diagnóstico do Azure](../virtual-machines/windows/extensions-diagnostics-template.md) ou a [extensão de diagnóstico do Azure do Linux](../virtual-machines/linux/diagnostic-extension.md) na VM convidada. Um motivo comum para usar métricas de convidado em vez de métricas de host é que as métricas de convidado fornecem uma seleção maior de métricas do que as métricas de host. Um exemplo é a métrica de consumo de memória, que só está disponível por meio de métricas de convidado. As métricas de host com suporte são listadas [aqui](../azure-monitor/platform/metrics-supported.md)e as métricas de convidado comumente usadas são listadas [aqui](../azure-monitor/platform/autoscale-common-metrics.md). Este artigo mostra como modificar o [modelo básico do conjunto de dimensionamento viável](virtual-machine-scale-sets-mvss-start.md) para usar regras de dimensionamento automático com base em métricas de convidado para conjuntos de dimensionamento do Linux.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

Em um [artigo anterior](virtual-machine-scale-sets-mvss-start.md) , criamos um modelo de conjunto de dimensionamento básico. Agora, usaremos esse modelo anterior e o modificaremos para criar um modelo que implanta um conjunto de dimensionamento do Linux com dimensionamento automático baseado em métrica de convidado.

Primeiro, adicione parâmetros para `storageAccountName` e `storageAccountSasToken`. O agente de diagnóstico armazena dados de métrica em uma [tabela](../cosmos-db/table-storage-how-to-use-dotnet.md) nesta conta de armazenamento. A partir do agente de diagnóstico do Linux versão 3,0, não há mais suporte para o uso de uma chave de acesso de armazenamento. Em vez disso, use um [token SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

Em seguida, modifique o conjunto de dimensionamento `extensionProfile` para incluir a extensão de diagnóstico. Nessa configuração, especifique a ID de recurso do conjunto de dimensionamento para coletar métricas, bem como a conta de armazenamento e o token SAS a serem usados para armazenar as métricas. Especifique com que frequência as métricas são agregadas (neste caso, a cada minuto) e quais métricas rastrear (nesse caso, porcentagem de memória usada). Para obter informações mais detalhadas sobre essa configuração e métricas diferentes da porcentagem de memória usada, consulte [esta documentação](../virtual-machines/linux/diagnostic-extension.md).

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

Por fim, adicione um recurso de `autoscaleSettings` para configurar o dimensionamento automático com base nessas métricas. Esse recurso tem uma cláusula `dependsOn` que faz referência ao conjunto de dimensionamento para garantir que o conjunto de dimensionamento exista antes de tentar fazer o dimensionamento automático. Se você escolher uma métrica diferente para o dimensionamento automático, use o `counterSpecifier` da configuração da extensão de diagnóstico como o `metricName` na configuração de dimensionamento automático. Para obter mais informações sobre configuração de dimensionamento automático, consulte as [práticas recomendadas de dimensionamento automático](../azure-monitor/platform/autoscale-best-practices.md) e a [documentação de referência da API REST do Azure monitor](/rest/api/monitor/autoscalesettings).

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
