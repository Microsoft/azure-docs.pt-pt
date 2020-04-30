---
title: Move Azure Public IP para outra região do Azure usando o portal Azure
description: Utilize o modelo do Gestor de Recursos Azure para mover o IP público azure de uma região do Azure para outra usando o portal Azure.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6dd4b3279fc0110fff2ee0397a785c87b63644d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147835"
---
# <a name="move-azure-public-ip-to-another-region-using-the-azure-portal"></a>Move Azure Public IP para outra região usando o portal Azure

Há vários cenários em que gostaria de mover os seus IPs públicos azure existentes de uma região para outra. Por exemplo, pode querer criar um IP público com a mesma configuração e sku para testes. Também pode querer mover um IP público para outra região como parte do planeamento de recuperação de desastres.

Os IPs públicos de Azure são específicos da região e não podem ser movidos de uma região para outra. No entanto, pode utilizar um modelo do Gestor de Recursos Azure para exportar a configuração existente de um IP público.  Em seguida, pode encenar o recurso noutra região exportando o IP público para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar o modelo para a nova região.  Para obter mais informações sobre o Gestor de Recursos e modelos, consulte [Quickstart: Crie e implemente modelos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)do Gestor de Recursos Azure utilizando o portal Azure .


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o IP público de Azure está na região de Azure, de onde pretende mover-se.

- Os IPs públicos de Azure não podem ser movidos entre regiões.  Terá de associar o novo IP público aos recursos na região alvo.

- Para exportar uma configuração ip pública e implementar um modelo para criar um IP público em outra região, você precisará do papel de Colaborador de Rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que está a utilizar. Este layout inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede (NSGs) e redes virtuais.

- Verifique se a sua subscrição Azure permite criar IPs públicos na região alvo que é utilizada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para apoiar a adição de IPs públicos para este processo.  Veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparar e mover
Os seguintes passos mostram como preparar o IP público para o movimento de configuração usando um modelo de Gestor de Recursos, e mover a configuração IP pública para a região alvo usando o portal Azure.

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportar o modelo e implementar a partir de um script

1. Inicie sessão nos > **grupos**de recursos do [portal Azure.](https://portal.azure.com)
2. Localize o Grupo de Recursos que contém a fonte de IP público e clique nele.
3. Selecione > Modelo**de exportação**de **definições** > .
4. Escolha **implementar** na lâmina do **modelo de exportação.**
5. Clique em **MODELO** > **Editar parâmetros** para abrir o ficheiro **parâmetros.json** no editor online.
8. Para editar o parâmetro do nome IP público, altere o imóvel com**valor** de **parâmetros** > a partir da fonte do nome IP público para o nome do seu IP público alvo, certifique-se de que o nome está em cotações:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  Clique em **Guardar** no editor.

9.  Clique no**modelo de edição** de **MODELO** > para abrir o ficheiro **template.json** no editor online.

10. Para editar a região alvo onde o IP público será movido, altere a propriedade de **localização** sob **recursos:**

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. Para obter códigos de localização da região, consulte [localizações azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código para uma região é o nome da região sem espaços,**central** **dos EUA** = central.

12. Também pode alterar outros parâmetros do modelo se escolher, e é opcional dependendo dos seus requisitos:

    * **Sku** - Pode alterar o sku do IP público na configuração de padrão para básico ou básico para padrão alterando a propriedade de**nome** **sku** > no ficheiro **template.json:**

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Para obter mais informações sobre as diferenças entre ips públicos básicos e standard, consulte [Criar, alterar ou eliminar um endereço IP público:](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)

    * **Método** de atribuição de IP público e **tempo limite de inativo** - Pode alterar ambas as opções no modelo alterando a propriedade **publicIPAllocationMethod** de **Dynamic** para **Static** ou **Static** to **Dynamic**. O tempo inativo pode ser alterado alterando a propriedade **idleTimeoutInMinutes** para a sua quantidade desejada.  O predefinido é **de 4:**

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Para obter mais informações sobre os métodos de atribuição e os valores de tempo inativo, consulte [Criar, alterar ou eliminar um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


13. Clique em **Guardar** no editor online.

14. Clique em**Subscrição** **BASICS** > para escolher a subscrição onde será implementado o IP público-alvo.

15. Clique no grupo **BASICS** > **Resource** para escolher o grupo de recursos onde será implementado o IP público alvo.  Pode clicar **em Criar um novo** grupo de recursos para o IP público alvo.  Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do IP público de origem existente.

16. Verifique se a**localização** **BASICS** > está definida para o local alvo onde deseja que o IP público seja implantado.

17. Verifique em **DEFINIÇÕES** que o nome corresponde ao nome que inseriu no editor de parâmetros acima.

18. Verifique a caixa em **termos e condições.**

19. Clique no botão **Comprar** para implementar o IP público alvo.

## <a name="discard"></a>Eliminar

Se desejar descartar o IP público alvo, elimine o grupo de recursos que contém o IP público alvo.  Para isso, selecione o grupo de recursos do seu painel de instrumentos no portal e selecione **Eliminar** na parte superior da página de visão geral.

## <a name="clean-up"></a>Limpeza

Para comprometer as alterações e completar a mudança do IP público, elimine a fonte pública IP ou grupo de recursos. Para isso, selecione o IP público ou o grupo de recursos do seu painel de instrumentos no portal e selecione **Eliminar** no topo de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um IP Público Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e a recuperação de desastres em Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
