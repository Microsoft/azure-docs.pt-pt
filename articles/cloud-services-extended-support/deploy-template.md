---
title: Implementar um Serviço de Nuvem Azure (suporte alargado) - Modelos
description: Implementar um Serviço de Nuvem Azure (suporte alargado) utilizando modelos ARM
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 1e2a0859227ad790763dc9ae07cb408a72538f90
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773373"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>Implementar um Serviço de Nuvem (suporte alargado) utilizando modelos ARM

Este tutorial explica como criar uma implementação de Cloud Service (suporte alargado) utilizando [modelos ARM](../azure-resource-manager/templates/overview.md). 

> [!IMPORTANT]
> Os Serviços cloud (suporte alargado) estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="before-you-begin"></a>Antes de começar

1. Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado) e crie os recursos associados.

2. Criar um novo grupo de recursos utilizando o [portal Azure](/azure/azure-resource-manager/management/manage-resource-groups-portal) ou [PowerShell](/azure/azure-resource-manager/management/manage-resource-groups-powershell). Este passo é opcional se estiver a utilizar um grupo de recursos existente.
 
3. Criar uma nova conta de armazenamento utilizando o [portal Azure](/azure/storage/common/storage-account-create?tabs=azure-portal) ou [PowerShell](/azure/storage/common/storage-account-create?tabs=azure-powershell). Este passo é opcional se estiver a utilizar uma conta de armazenamento existente.

4. Faça o upload dos ficheiros de Definição de Serviço (.csdef) e Configuração de Serviço (.cscfg) para a conta de armazenamento utilizando o [portal Azure](/azure/storage/blobs/storage-quickstart-blobs-portal#upload-a-block-blob), [AzCopy](/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json) ou [PowerShell](/azure/storage/blobs/storage-quickstart-blobs-powershell#upload-blobs-to-the-container). Obtenha os URIs SAS de ambos os ficheiros a adicionar ao modelo ARM mais tarde neste tutorial.

5. (Opcional) Crie um cofre chave e carre fique com os certificados.

    -  Os certificados podem ser anexados aos serviços na nuvem para permitir uma comunicação segura de e para o serviço. Para utilizar certificados, as suas impressões digitais devem ser especificadas no seu ficheiro de Configuração de Serviço (.cscfg) e enviadas para um cofre de chaves. Um cofre-chave pode ser criado através do [portal Azure](/azure/key-vault/general/quick-create-portal) ou [PowerShell](/azure/key-vault/general/quick-create-powershell).
    - O cofre-chave associado deve estar localizado na mesma região e subscrição que o serviço de nuvem.
    - O cofre-chave associado deve ser ativado permissões apropriadas para que o recurso Cloud Services (suporte alargado) possa obter certificados do Key Vault. Para mais informações, consulte [Certificados e Cofre chave](certificates-and-key-vault.md)
    - O cofre-chave deve ser referenciado na secção OsProfile do modelo ARM indicado nos degraus abaixo.

## <a name="deploy-a-cloud-service-extended-support"></a>Implementar um Serviço de Cloud (suporte alargado)

> [!NOTE]
> Uma forma mais fácil e rápida de gerar o seu modelo ARM e o seu ficheiro de parâmetros é através do [portal Azure](https://portal.azure.com). Pode [descarregar o modelo ARM gerado](generate-template-portal.md) através do portal para criar o seu Serviço cloud via Powershell
 
1. Criar rede virtual. O nome da rede virtual deve coincidir com as referências no ficheiro Configuração de Serviço (.cscfg). Se utilizar uma rede virtual existente, omita esta secção do modelo ARM.

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     Se criar uma nova rede virtual, adicione o seguinte à `dependsOn` secção para garantir que a plataforma cria a rede virtual antes de criar o serviço na nuvem.

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. Crie um endereço IP público e (opcionalmente) desaponhe a propriedade da etiqueta DNS do endereço IP público. Se estiver a utilizar um IP estático, precisa de o referir como um ficheiro IP reservado na configuração de serviço (.cscfg). Se utilizar um endereço IP existente, ignore este passo e adicione as informações do endereço IP diretamente nas definições de configuração do balançador de carga do seu modelo ARM.
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     Se criar um novo endereço IP, adicione o seguinte à `dependsOn` secção para garantir que a plataforma cria o endereço IP antes de criar o serviço de cloud.
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. Crie um Objeto de Perfil de Rede e associe o endereço IP público à parte frontal do equilibrador de carga. Um equilibrador de carga é automaticamente criado pela plataforma.

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

4. Adicione a referência do cofre chave na  `OsProfile`   secção do modelo ARM. O Key Vault é utilizado para armazenar certificados associados aos Serviços Cloud (suporte alargado). Adicione os certificados ao Key Vault e, em seguida, faça referência às impressões digitais do certificado no ficheiro de Configuração de Serviço (.cscfg). Também precisa de ativar o Key Vault para obter permissões apropriadas para que o recurso Cloud Services (suporte alargado) possa obter o certificado armazenado como segredos do Key Vault. O cofre-chave deve estar localizado na mesma região e subscrição que o serviço de nuvem e ter um nome único. Para obter mais informações, consulte [a utilização de certificados com serviços cloud (suporte alargado)](certificates-and-key-vault.md).
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > SourceVault é o ID de recursos ARM do seu cofre de chaves. Pode encontrar esta informação localizando o ID de recurso na secção de propriedades do seu cofre de chaves.
    > - certificateUrl pode ser encontrado navegando no certificado no cofre-chave rotulado como **Identificador Secreto**.  
   >  - certificateUrl deve ser do formulário https://{keyvault-endpoin}/secrets/{secretname}/{secret-id}

5. Criar um perfil de papel. Certifique-se de que o número de funções, nomes de funções, número de casos em cada função e tamanhos são os mesmos em toda a Configuração de Serviço (.cscfg), Definição de Serviço (.csdef) e secção de perfil de função no modelo ARM.
    
    ```json
    "roleProfile": {
      "roles": {
        "value": [
          {
            "name": "WebRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            }
          },
          {
            "name": "WorkerRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            } 
          } 
        ]
      }
    }   
    ```

6. (Opcional) Crie um perfil de extensão para adicionar extensões ao seu serviço na nuvem. Para este exemplo, estamos a adicionar o ambiente de trabalho remoto e a extensão de diagnóstico do Windows Azure.
   > [!Note] 
   > A palavra-passe para ambiente de trabalho remoto deve ter entre 8-123 caracteres de comprimento e deve satisfazer pelo menos 3 requisitos de complexidade da palavra-passe a partir do seguinte: 1) Contém um caracteres maiúsculas 2) Contém um caracteres minúsculos 3) Contém um dígito numérico 4) Contém um carácter especial 5) Não são permitidos caracteres de controlo

    ```json
        "extensionProfile": {
          "extensions": [
            {
              "name": "RDPExtension",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Windows.Azure.Extensions",
                "type": "RDP",
                "typeHandlerVersion": "1.2.1",
                "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
              }
            },
            {
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Azure.Diagnostics",
                "type": "PaaSDiagnostics",
                "typeHandlerVersion": "1.5",
                "settings": "[parameters('wadPublicConfig_WebRole1')]",
                "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                "rolesAppliedTo": [
                  "WebRole1"
                ]
              }
            }
          ]
        }
    ```

7. Reveja o modelo completo.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
        },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2020-10-01-preview",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
            "roleProfile": {
              "roles": [
                {
                  "name": "WebRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                },
                {
                  "name": "WorkerRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                }
              ]
            },
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                  ]
                }
              }
            ]
          }
        }
       }
      ]
    }
    ```

8. Implemente o ficheiro do modelo e do parâmetro (definindo parâmetros no ficheiro do modelo) para criar a implementação do Serviço de Nuvem (suporte alargado). Consulte estes [modelos de amostra](https://github.com/Azure-Samples/cloud-services-extended-support) conforme necessário.

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "ContosOrg" -TemplateFile "file path to your template file" -TemplateParameterFile "file path to your parameter file"
    ```

## <a name="next-steps"></a>Passos seguintes 

- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).
- Visite o [repositório](https://github.com/Azure-Samples/cloud-services-extended-support) de amostras cloud services (suporte alargado)
