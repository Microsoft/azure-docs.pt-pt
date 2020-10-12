---
title: Mover a configuração IP pública do Azure para outro portal Azure da região do Azure
description: Utilize um modelo para mover a configuração IP pública do Azure de uma região de Azure para outra utilizando o portal Azure.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 23fe515ddfdecb9ef168dd662e3fa2d91ece688f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711481"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>Mover a configuração IP pública do Azure para outra região usando o portal Azure

Existem vários cenários em que você gostaria de mover as suas configurações de IP público azure existentes de uma região para outra. Por exemplo, pode querer criar um IP público com a mesma configuração e sku para testes. Você também pode querer mover uma configuração ip pública para outra região como parte do planeamento de recuperação de desastres.

**Os IPs públicos do Azure são específicos da região e não podem ser movidos de uma região para outra.** No entanto, pode utilizar um modelo de Gestor de Recursos Azure para exportar a configuração existente de um IP público.  Em seguida, pode encenar o recurso noutra região exportando o IP público para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar o modelo para a nova região.  Para obter mais informações sobre o Gestor de Recursos e modelos, consulte [Quickstart: Criar e implementar modelos de Gestor de Recursos Azure utilizando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o Azure Public IP se encontra na região de Azure, de onde pretende mover-se.

- Os IPs públicos do Azure não podem ser movidos entre regiões.  Terá de associar o novo IP público aos recursos na região alvo.

- Para exportar uma configuração IP pública e implementar um modelo para criar um IP público noutra região, você precisará do papel de Contribuinte de Rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que está a usar. Este layout inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede (NSGs) e redes virtuais.

- Verifique se a sua subscrição Azure permite criar IPs públicos na região alvo que é utilizada. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar a adição de IPs públicos para este processo.  Veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparar e mover
Os passos seguintes mostram como preparar o IP público para o movimento de configuração usando um modelo de Gestor de Recursos e mover a configuração IP pública para a região alvo usando o portal Azure.

### <a name="export-the-template-and-deploy-from-a-script"></a>Exporte o modelo e implemente a partir de um script

1. Iniciar sessão no [portal Azure](https://portal.azure.com)  >  **Resource Groups**.
2. Localize o Grupo de Recursos que contém o IP público de origem e clique nele.
3. Selecione > **Definições**  >  **Modelo de exportação**.
4. Escolha **Implementar** na lâmina do **modelo de exportação.**
5. Clique **em modelo**  >  **Editar parâmetros** para abrir oparameters.js**no** ficheiro no editor online.
8. Para editar o parâmetro do nome IP público, altere o valor da propriedade sob **parâmetros**  >  **value** do nome IP público de origem para o nome do seu IP público alvo, certifique-se de que o nome está em aspas:

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

9.  Clique no modelo de edição **do modelo**para abrir otemplate.js >  **Edit template** **no** ficheiro no editor online.

10. Para editar a região-alvo onde o IP público será movido, altere o imóvel de **localização** em **recursos**:

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

11. Para obter códigos de localização da região, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/)  O código para uma região é o nome da região sem espaços, **central dos EUA central.**  =  **centralus**

12. Também pode alterar outros parâmetros no modelo se escolher, e é opcional dependendo dos seus requisitos:

    * **Sku** - Pode alterar o sku do IP público na configuração de padrão para básico ou básico para padrão, alterando a propriedade do nome **sku**  >  **name** no **template.jsno** ficheiro:

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

        Para obter mais informações sobre as diferenças entre as ips públicas básicas e padrão sku, consulte [Criar, alterar ou eliminar um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address):

    * **Método de atribuição de IP público** e tempo limite de marcha **lenta** - Pode alterar ambas as opções no modelo alterando a propriedade **publicIPAllocationMethod** de **Dynamic** para **Static** ou **Static** to **Dynamic**. O tempo limite de marcha lenta pode ser alterado alterando a propriedade **idleTimeoutInMinutes** para a quantidade desejada.  O padrão é **4:**

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

        Para obter mais informações sobre os métodos de atribuição e os valores de tempo limite inativo, consulte [Criar, alterar ou eliminar um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


13. Clique em **Guardar** no editor online.

14. Clique **em BASICS**  >  **Subscription** para escolher a subscrição onde o IP público-alvo será implementado.

15. Clique **em BASICS**  >  **Resource group** para escolher o grupo de recursos onde o IP público-alvo será implementado.  Pode clicar em **Criar novo** para criar um novo grupo de recursos para o IP público-alvo.  Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do IP público de origem existente.

16. Verifique **o BASICS**  >  **A localização** está definida para o local alvo onde deseja que o IP público seja implantado.

17. Verifique em **DEFINIÇÕES** que o nome corresponde ao nome que introduziu no editor de parâmetros acima.

18. Verifique a caixa em **termos e condições.**

19. Clique no botão **'Comprar'** para implementar o IP público-alvo.

## <a name="discard"></a>Eliminar

Se desejar descartar o IP público-alvo, elimine o grupo de recursos que contém o IP público-alvo.  Para tal, selecione o grupo de recursos do seu painel de instrumentos no portal e selecione **Eliminar** no topo da página geral.

## <a name="clean-up"></a>Limpeza

Para cometer as alterações e completar o movimento do IP público, elimine o IP público de origem ou grupo de recursos. Para tal, selecione o IP público ou o grupo de recursos do seu painel de instrumentos no portal e selecione **Eliminar** no topo de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um IP público do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou subscrição](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
