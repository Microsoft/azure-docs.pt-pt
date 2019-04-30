---
title: Utilizar o dimensionamento automático do Azure com as métricas de convidado num modelo de conjunto de dimensionamento do Linux | Documentos da Microsoft
description: Saiba como Dimensionar automaticamente com as métricas de convidado num modelo de conjunto de dimensionamento de Máquina Virtual do Linux
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: manayar
ms.openlocfilehash: deddcc8623803f9d003f3fafcef5252ebd34b813
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803354"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Dimensionamento automático através de métricas de convidado num modelo de conjunto de dimensionamento do Linux

Existem dois tipos de métricas no Azure, que são recolhidos das VMs e conjuntos de dimensionamento: algumas são provenientes da VM de anfitrião e outras pessoas vêm de VM do convidado. Num alto nível, se estiver a utilizar o padrão CPU, disco e métricas de rede, em seguida, métricas de anfitrião são provavelmente uma boa opção. Se, no entanto, terá uma seleção maior de métricas, em seguida, as métricas de convidado são provavelmente a melhor opção. Vamos dar uma olhada nas diferenças entre os dois:

Métricas de anfitrião são mais simples e mais confiável. Não necessitam de configuração adicional porque eles são coletados pelo anfitrião de VM, ao passo que as métricas de convidado exigem a instalação do [extensão do Windows Azure Diagnostics](../virtual-machines/windows/extensions-diagnostics-template.md) ou o [extensão de diagnóstico do Linux do Azure](../virtual-machines/linux/diagnostic-extension.md)na VM do convidado. Uma das razões comuns para utilizar as métricas de convidado em vez de métricas de anfitrião é que as métricas de convidado oferecem uma seleção maior de métricas de métricas de anfitrião. Um exemplo desse tipo é métricas de consumo de memória, que só estão disponíveis por meio de métricas de convidado. As métricas de anfitrião suportados estão listadas [aqui](../azure-monitor/platform/metrics-supported.md), e as métricas de convidado usadas são listadas [aqui](../azure-monitor/platform/autoscale-common-metrics.md). Este artigo mostra como modificar o [modelo de conjunto de dimensionamento mínimo viável](./virtual-machine-scale-sets-mvss-start.md) utilizar regras de dimensionamento automático com base em métricas de convidado para conjuntos de dimensionamento do Linux.

## <a name="change-the-template-definition"></a>Altere a definição do modelo

Pode ser visto o modelo de conjunto de dimensionamento mínimo viável [aqui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), e o modelo para implementar o Linux conjunto de dimensionamento com o dimensionamento automático baseado em convidado pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Vamos examinar o diff utilizado para criar este modelo (`git diff minimum-viable-scale-set existing-vnet`) parte por parte:

Primeiro, adicione parâmetros para `storageAccountName` e `storageAccountSasToken`. O agente de diagnóstico armazena dados de métricos num [tabela](../cosmos-db/table-storage-how-to-use-dotnet.md) nesta conta de armazenamento. A partir do agente de diagnóstico do Linux versão 3.0, com uma chave de acesso de armazenamento já não é suportada. Em alternativa, utilize um [Token de SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

Em seguida, modifique o conjunto de dimensionamento `extensionProfile` para incluir a extensão de diagnóstico. Nesta configuração, especifique o ID de recurso de conjunto de dimensionamento para recolher métricas, bem como a conta de armazenamento e o SAS token para utilizar para armazenar as métricas. Especifique a frequência com que as métricas são agregadas (no caso, a cada minuto) e as métricas para controlar (neste caso, percentagem memória utilizada). Para obter informações detalhadas sobre esta configuração e as métricas que não seja a percentagem de memória utilizada, consulte [esta documentação](../virtual-machines/linux/diagnostic-extension.md).

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

Por fim, adicione um `autoscaleSettings` recursos para configurar o dimensionamento automático com base nessas métricas. Este recurso tem um `dependsOn` cláusula que faça referência a escala definida para garantir que o conjunto de dimensionamento existe antes de tentar para dimensionar automaticamente. Se optar por uma métrica diferente para dimensionamento automático no, tem de utilizar o `counterSpecifier` da configuração de extensão de diagnóstico, como o `metricName` na configuração do dimensionamento automático. Para obter mais informações sobre a configuração de dimensionamento automático, consulte a [melhores práticas de dimensionamento automático](..//azure-monitor/platform/autoscale-best-practices.md) e o [documentação de referência da API de REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931928.aspx).

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





## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
