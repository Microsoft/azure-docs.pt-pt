---
title: Move Azure network security group (NSG) para outra região de Azure usando o portal Azure
description: Utilize o modelo do Gestor de Recursos Azure para mover o grupo de segurança da rede Azure de uma região do Azure para outra utilizando o portal Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: dce267178c3caf813ccdcac4bba86ccfde3f3421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647191"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Move Azure network security group (NSG) para outra região usando o portal Azure

Há vários cenários em que você gostaria de mover os seus NSGs existentes de uma região para outra. Por exemplo, pode querer criar um NSG com as mesmas regras de configuração e segurança para testes. Você também pode querer mover um NSG para outra região como parte do planeamento de recuperação de desastres.

Os grupos de segurança azure não podem ser transferidos de uma região para outra. No entanto, pode utilizar um modelo do Gestor de Recursos Azure para exportar as regras de configuração e segurança existentes de um NSG.  Em seguida, pode encenar o recurso em outra região exportando o NSG para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar o modelo para a nova região.  Para obter mais informações sobre o Gestor de Recursos e modelos, consulte [Quickstart: Crie e implemente modelos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)do Gestor de Recursos Azure utilizando o portal Azure .


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o grupo de segurança da rede Azure está na região de Azure, de onde pretende mover-se.

- Os grupos de segurança da rede Azure não podem ser deslocados entre regiões.  Terá de associar o novo NSG aos recursos na região alvo.

- Para exportar uma configuração DE NSG e implementar um modelo para criar um NSG em outra região, você precisará do papel de Colaborador de Rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que está a utilizar. Este layout inclui, mas não se limita a equilibradores de carga, IPs públicos e redes virtuais.

- Verifique se a sua subscrição Azure permite criar NSGs na região alvo que é utilizada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição de NSGs para este processo.  Veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparar e mover
Os seguintes passos mostram como preparar o grupo de segurança da rede para o movimento de configuração e regra de segurança usando um modelo de Gestor de Recursos, e mover as regras de configuração e segurança do NSG para a região alvo usando o portal.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Exportar o modelo e implantar a partir do portal

1. Inicie sessão nos > **grupos**de recursos do [portal Azure.](https://portal.azure.com)
2. Localize o Grupo de Recursos que contém a fonte NSG e clique nele.
3. Selecione > Modelo**de exportação**de **definições** > .
4. Escolha **implementar** na lâmina do **modelo de exportação.**
5. Clique em **MODELO** > **Editar parâmetros** para abrir o ficheiro **parâmetros.json** no editor online.
6. Para editar o parâmetro do nome NSG, altere a propriedade de **valor** em **parâmetros:**

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Mude o valor nsg de origem no editor para um nome da sua escolha para o NSG alvo. Certifique-se de que encerra o nome em aspas.

8.  Clique em **Guardar** no editor.

9.  Clique no**modelo de edição** de **MODELO** > para abrir o ficheiro **template.json** no editor online.

10. Para editar a região alvo onde serão movidas as regras de configuração e segurança do NSG, altere a propriedade de **localização** sob **recursos** no editor online:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. Para obter códigos de localização da região, consulte [localizações azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código para uma região é o nome da região sem espaços,**central** **dos EUA** = central.

12. Também pode alterar outros parâmetros do modelo se escolher, e é opcional dependendo dos seus requisitos:

    * **Regras de segurança** - Pode editar quais as regras que são implementadas no NSG alvo adicionando ou removendo regras à secção **de regras** de segurança No ficheiro **template.json:**

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      Para completar a adição ou a remoção das regras no NSG alvo, deve também editar os tipos de regras personalizados no final do ficheiro **template.json** no formato do exemplo abaixo:

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. Clique em **Guardar** no editor online.

14. Clique em**Subscrição** **BASICS** > para escolher a subscrição onde o NSG alvo será implementado.

15. Clique no grupo **BASICS** > **Resource** para escolher o grupo de recursos onde o NSG alvo será implantado.  Pode clicar **em Criar um novo** grupo de recursos para o NSG alvo.  Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do NSG existente.

16. Verifique se a**localização** **BASICS** > está definida para o local alvo onde deseja que o NSG seja implantado.

17. Verifique em **DEFINIÇÕES** que o nome corresponde ao nome que inseriu no editor de parâmetros acima.

18. Verifique a caixa em **termos e condições.**

19. Clique no botão **Comprar** para implantar o grupo de segurança da rede alvo.

## <a name="discard"></a>Eliminar

Se desejar descartar o NSG alvo, elimine o grupo de recursos que contém o NSG alvo.  Para isso, selecione o grupo de recursos do seu painel de instrumentos no portal e selecione **Eliminar** na parte superior da página de visão geral.

## <a name="clean-up"></a>Limpeza

Para comprometer as alterações e completar o movimento do NSG, elimine a fonte NSG ou o grupo de recursos. Para isso, selecione o grupo de segurança da rede ou o grupo de recursos do seu painel de instrumentos no portal e selecione **Eliminar** no topo de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um grupo de segurança da rede Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e a recuperação de desastres em Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
