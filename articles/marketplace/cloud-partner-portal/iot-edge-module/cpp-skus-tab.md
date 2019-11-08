---
title: SKUs do módulo Azure IoT Edge | Azure Marketplace
description: Crie SKUs para um módulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 230f3d6438d44c4e1e1721c0cb1453c85958e282
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813852"
---
# <a name="iot-edge-module-skus-tab"></a>Guia SKUs do módulo IoT Edge

A guia **SKUs** da página **nova oferta** permite que você crie uma ou mais SKUs e associe-as à sua nova oferta.  Você pode usar SKUs diferentes para diferenciar uma solução por conjuntos de recursos, modelos de cobrança ou alguma outra característica.


## <a name="sku-settings"></a>Configurações de SKU

Quando você começa a criar uma nova oferta, não há SKUs associadas à oferta. Para criar uma nova SKU, siga estas etapas:

- Na página **IOT Edge módulos > nova oferta** , selecione a guia **SKUs** .
- Em SKUs, selecione **+ novo SKU** para abrir uma caixa de diálogo.

  ![Novo botão SKU na guia nova oferta para módulos de IoT Edge](./media/iot-edge-module-skus-tab-new-sku.png)

- Na caixa de diálogo **novo SKU** , insira um identificador para a SKU e, em seguida, selecione **OK**.
(A tabela a seguir fornece as convenções de nomenclatura do identificador.)

A guia **SKUs** é atualizada e exibe os campos que você edita para configurar a SKU. Um asterisco (*) acrescentado ao nome do campo indica que ele é necessário.

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID do SKU\***       | Identificador para este SKU. Esse nome tem um máximo de 50 caracteres, que consistem em caracteres alfanuméricos minúsculos ou traços (-), mas não podem terminar com um traço. **Observação:** Não é possível alterar esse nome após a publicação da oferta. O nome é publicamente visível em URLs de produto. |
|  |  |


## <a name="sku-details"></a>Detalhes do SKU

Configure os **detalhes da SKU** para definir como seu SKU será exibido nos sites do Azure Marketplace e do portal do Azure.

![Metadados de SKU do módulo IoT Edge](media/iot-edge-module-skus-tab-metadata.png)

A tabela a seguir descreve a finalidade, o conteúdo e a formatação dos campos em **detalhes do SKU**. Os campos obrigatórios são indicted por um asterisco (*).

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **Título\***        | Título desta SKU. Comprimento máximo de 50 caracteres. <br/> Ele será mostrado no portal do Azure e será usado como um nome de módulo padrão (sem espaços e caracteres especiais) quando for implantado. Consulte as imagens abaixo para ver exatamente onde esse campo é exibido.|
| **\* de resumo**      | Breve resumo deste SKU. Comprimento máximo de 100 caracteres. **Não** Resuma a oferta, apenas a SKU.  Este resumo será mostrado no Azure Marketplace. Consulte as imagens abaixo para ver exatamente onde esse campo é exibido.|
| **Descrição\***  | Breve descrição deste SKU. Comprimento máximo de 3000 caracteres. Não descreva a oferta, mas apenas esta SKU. Ele será mostrado no Azure Marketplace e no portal do Azure. No portal do Azure, ele será anexado à descrição do Marketplace que descreve a oferta definida na guia Marketplace.  Pode ser o mesmo que o resumo de SKU. Consulte as imagens abaixo para ver exatamente onde esse campo é exibido.|
| **Ocultar este\* de SKU** | Mantenha a configuração padrão, que é **não**. |
|  |  |


### <a name="sku-example"></a>Exemplo de SKU

 Os exemplos a seguir mostram como os campos **título**, **Resumo**e **Descrição** do SKU aparecem em modos de exibição diferentes.
 

#### <a name="on-the-azure-marketplace-website"></a>No site do Azure Marketplace:

- Ao examinar os detalhes do SKU:

    ![Como os SKUs são exibidos no site do Azure Marketplace](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>No site do portal do Azure:

- Ao procurar SKUs:

    ![Como IoT Edge módulo aparece ao navegar na portal do Azure #1](media/iot-edge-module-portal-browse.png)

    ![Como IoT Edge módulo aparece ao navegar na portal do Azure #2](media/iot-edge-module-portal-product-picker.png)

- Ao pesquisar SKUs:

    ![Como IoT Edge módulo aparece ao pesquisar o portal do Azure](media/iot-edge-module-portal-search.png)

- Ao examinar os detalhes do SKU:

    ![Como IoT Edge módulo aparece ao procurar os detalhes do produto no portal](./media/iot-edge-module-portal-pdp.png)

- Ao implantar o módulo:
    
    ![Como IoT Edge módulo aparece quando está sendo implantado](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>Conteúdo do SKU

Em **imagens do módulo de borda**, forneça as informações necessárias para carregar seu módulo IOT Edge.

Dê-nos acesso ao seu ACR ( [registro de contêiner do Azure](https://azure.microsoft.com/services/container-registry/) ) que contém a imagem do módulo IOT Edge para que possamos carregá-lo e certificá-lo. Depois de publicado, seu módulo de IoT Edge será copiado e distribuído usando um registro de contêiner público hospedado pelo Azure Marketplace.

Você pode direcionar várias plataformas e fornecer várias versões por meio de marcas. Saiba mais sobre [marcas e controle de versão em "preparar seu IOT Edge de ativos técnicos do módulo"](./cpp-create-technical-assets.md).

![Imagens do módulo IoT Edge](./media/iot-edge-module-skus-tab-acr.png)

A tabela a seguir descreve a finalidade, o conteúdo e a formatação dos campos para as seções **detalhes do repositório de imagens** e versão da **imagem**.  Os campos obrigatórios são indicted por um asterisco (*).


|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Detalhes do repositório de imagens***    |
| **ID da assinatura\***        | A ID de assinatura do Azure do ACR.|
| **Nome do grupo de recursos\***      | O nome do grupo de recursos do ACR.|
| **Nome do registro\***  | O nome do registro ACR. Copie apenas o nome do registro, não o nome do servidor de logon (por exemplo, sem o `azurecr.io`.) |
| **Nome do repositório\***  | O nome do repositório do ACR que contém o módulo IoT Edge. **Observação:** Depois que o nome é definido, ele não pode ser alterado posteriormente. Use um nome exclusivo para garantir que nenhuma outra oferta em sua conta tenha o mesmo nome. |
| **Nome de usuário\*** | O nome de usuário associado ao ACR (nome de usuário do administrador). |
| **\* de senha** | A senha associada ao ACR. |
|    |  ***Versão da imagem***   |
| **\* de marca de imagem ou Resumo** | Ele deve pelo menos incluir uma marca de `latest` e uma marca de versão (por exemplo, começando com `xx.xx.xx-` em que XX é um número). Eles devem ser [marcas de manifesto](https://github.com/estesp/manifest-tool) para direcionar várias plataformas. Todas as marcas referenciadas por uma marca de manifesto também devem ser adicionadas para que possamos carregá-las. Você pode adicionar várias versões de um módulo IoT Edge usando marcas. Todas as marcas de manifesto (exceto `latest`) devem começar com `X.Y-` ou `X.Y.Z-` em que X, Y, Z são inteiros. Saiba mais sobre [marcas e controle de versão em "preparar seu IOT Edge de ativos técnicos do módulo"](./cpp-create-technical-assets.md). <br/> Por exemplo, se uma marca de `latest` aponta para que aponta para `1.0.1-linux-x64`, `1.0.1-linux-arm32`, e `1.0.1-windows-arm32`, essas 6 marcas precisam ser adicionadas aqui. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Ajude seus clientes a iniciarem usando as configurações padrão

Defina as configurações mais comuns para implantar o módulo IoT Edge. Otimize implantações de clientes, permitindo que elas iniciem seu IoT Edge módulo pronta para uso com esses padrões.

![IoT Edge configurações padrão do módulo na implantação](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

A tabela a seguir descreve a finalidade, o conteúdo e a formatação dos campos para **rotas padrão**, **as propriedades padrão**de "bificar-se", as **variáveis de ambiente padrão**e as **criaoptions padrão**.

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **Rotas padrão**        | Cada nome de rota padrão e o valor devem ter menos de 512 caracteres. Você pode definir até 5 rotas padrão. Certifique-se de usar uma [sintaxe de rota](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) correta em seu valor de rota. Para fazer referência ao seu módulo, use seu nome de módulo padrão, que será o seu **título de SKU** sem espaços e caracteres especiais. Para consultar outros módulos ainda não conhecidos, use a Convenção de `<FROM_MODULE_NAME>` para permitir que seus clientes saibam que eles precisam atualizar essas informações. Saiba mais sobre [rotas de IOT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Por exemplo, se o módulo `ContosoModule` escuta entradas em dados de `ContosoInput` e de saída em `ContosoOutput`, faz sentido definir as duas rotas padrão a seguir:<br/>-Nome #1: `ToContosoModule`<br/>-Valor #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>-Nome #2: `FromContosoModuleToCloud`<br/>-Valor #2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Propriedades desejadas de entrelaçamento padrão**      | Cada nome e valor de propriedades desejadas de entrelaçamento padrão deve ter menos de 512 caracteres. Você pode definir até cinco propriedades desejadas de 5 nome/valor. Valores de propriedades bidimensionais desejadas devem ser JSON válido, sem escape, sem matrizes e com uma hierarquia aninhada máxima de 4. Saiba mais sobre [as propriedades desejadas de entrelaçamento](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Por exemplo, se um módulo oferecer suporte a uma taxa de atualização configurável dinamicamente por meio de propriedades desejadas de entrelaçamento, faz sentido definir a seguinte propriedade de entrelaçamento padrão:<br/> -Nome #1: `RefreshRate`<br/>-Valor #1: `60`|
| **Variáveis de ambiente padrão**  | Cada nome e valor das variáveis de ambiente padrão devem ter menos de 512 caracteres. Você pode definir até cinco variáveis de ambiente de nome/valor. <br/>Por exemplo, se um módulo exigir que o aceite termos de uso antes de ser iniciado, você poderá definir a seguinte variável de ambiente:<br/> -Nome #1: `ACCEPT_EULA`<br/>-Valor #1: `Y`|
| **Criaroptions padrão**  | O createOptions deve ter menos de 512 caracteres. Ele deve ser um JSON válido, sem escape. Saiba mais sobre [criaroptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Por exemplo, se um módulo exigir associar uma porta, você poderá definir as seguintes opções de criar:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Selecione **salvar** para salvar as configurações de SKU. 


## <a name="next-steps"></a>Passos seguintes

Use a [guia Marketplace](./cpp-marketplace-tab.md) para criar uma descrição do Marketplace para sua oferta.
