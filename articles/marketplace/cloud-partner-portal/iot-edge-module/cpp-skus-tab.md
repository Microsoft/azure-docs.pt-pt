---
title: Módulo Azure IoT Edge SKUs [ Módulo De Borda Azul] Mercado Azure
description: Crie SKUs para um módulo IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: dsindona
ms.openlocfilehash: 49ae69263735d6ec35cd911a20100472b32c0b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286562"
---
# <a name="iot-edge-module-skus-tab"></a>Tab SKUs módulo IoT Edge

O separador **SKUs** da página **New Offer** permite-lhe criar uma ou mais SKUs e associá-las à sua nova oferta.  Pode utilizar diferentes SKUs para diferenciar uma solução por conjuntos de funcionalidades, modelos de faturação ou alguma outra característica.


## <a name="sku-settings"></a>Definições SKU

Quando começa a criar uma nova oferta, não há SKUs associados à oferta. Para criar um novo SKU, siga estes passos:

- Nos **módulos IoT Edge > página New Offer,** selecione o separador **SKUs.**
- Em SKUs, selecione **+ Novo SKU** para abrir uma caixa de diálogo.

  ![Novo botão SKU no separador New Offer para módulos IoT Edge](./media/iot-edge-module-skus-tab-new-sku.png)

- Na caixa de diálogo **New SKU,** introduza um identificador para o SKU e, em seguida, selecione **OK**.
(O quadro seguinte dá ao identificador convenções de nomeação.)

O separador **SKUs** é refrescado e exibe os campos que edita para configurar o SKU. Um asterisco (*) anexado ao nome de campo indica que é necessário.

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU ID\***       | Identificador para este SKU. Este nome tem um máximo de 50 caracteres, compostopor caracteres alfanuméricos inferiores ou traços (-), mas não pode terminar com um traço. **Nota:** Não pode mudar este nome depois da oferta ser publicada. O nome é publicamente visível nos URLs do produto. |
|  |  |


## <a name="sku-details"></a>Detalhes da SKU

Configure os **Detalhes SKU** para definir como o seu SKU será exibido nos websites do Azure Marketplace e do Portal Azure.

![IoT Edge módulo sku metadados](media/iot-edge-module-skus-tab-metadata.png)

A tabela que se segue descreve o propósito, o conteúdo e a formatação para campos sob **detalhes sKU**. Os campos necessários são indiciados por um asterisco (*).

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **Título\***        | Título para este SKU. Comprimento máximo de 50 caracteres. <br/> Será mostrado no Portal Azure e será usado como um nome de módulo padrão (sem espaços e caracteres especiais) quando for implantado. Veja as imagens abaixo para ver exatamente onde este campo é exibido.|
| **Resumo\***      | Resumo curto deste SKU. Comprimento máximo de 100 caracteres. **NÃO** sintetiza a oferta, apenas o SKU.  Este resumo será mostrado no Mercado Azure. Veja as imagens abaixo para ver exatamente onde este campo é exibido.|
| **Descrição\***  | Breve descrição deste SKU. Comprimento máximo de 3000 caracteres. Não descreva a oferta, mas apenas este SKU. Será exibido no mercado azul e no portal Azure. No portal Azure, será anexado à Descrição do Mercado descrevendo a oferta definida no separador Marketplace.  Pode ser o mesmo que o Resumo SKU. Veja as imagens abaixo para ver exatamente onde este campo é exibido.|
| **Esconda este SKU\*** | Mantenha a definição predefinida, que é **Nº .** |
|  |  |


### <a name="sku-example"></a>Exemplo sKU

 Os exemplos seguintes mostram como os campos SKU **Title,** **Resumo**e **Descrição** aparecem em diferentes pontos de vista.
 

#### <a name="on-the-azure-marketplace-website"></a>No site do Azure Marketplace:

- Ao analisar os detalhes do SKU:

    ![Como as SKUs são exibidas no site do Azure Marketplace](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>No site do Portal Azure:

- Ao navegar em SKUs:

    ![Como o módulo IoT Edge aparece ao navegar no portal Azure #1](media/iot-edge-module-portal-browse.png)

    ![Como o módulo IoT Edge aparece ao navegar no portal Azure #2](media/iot-edge-module-portal-product-picker.png)

- Ao procurar SKUs:

    ![Como o módulo IoT Edge aparece ao pesquisar o portal Azure](media/iot-edge-module-portal-search.png)

- Ao analisar os detalhes do SKU:

    ![Como o módulo IoT Edge aparece ao olhar para os detalhes do produto no portal](./media/iot-edge-module-portal-pdp.png)

- Ao implantar o módulo:
    
    ![Como o módulo IoT Edge aparece quando é implantado](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>Conteúdo SKU

Em Imagens de **Módulo sinuoso**, forneça a informação de que precisamos para carregar o módulo IoT Edge.

Dê-nos acesso ao seu Registo de [Contentores Azure](https://azure.microsoft.com/services/container-registry/) (ACR) que contém a sua imagem de módulo IoT Edge para que possamos carregá-lo e certificá-lo. Depois de publicado, o seu módulo IoT Edge será copiado e distribuído através de um registo público de contentores hospedado pelo Azure Marketplace.

Pode direcionar várias plataformas e fornecer várias versões através de tags. Saiba mais sobre [tags e versão em "Prepare os seus ativos técnicos do módulo IoT Edge".](./cpp-create-technical-assets.md)

![Imagens do módulo IoT Edge](./media/iot-edge-module-skus-tab-acr.png)

A tabela seguinte descreve o propósito, o conteúdo e a formatação dos campos para as secções Detalhes de **Repositório** de Imagem e **Versão de Imagem**.  Os campos necessários são indiciados por um asterisco (*).


|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Detalhes do Repositório de Imagem***    |
| **ID de subscrição\***        | O ID de subscrição Azure do seu ACR.|
| **Nome do grupo de recursos\***      | O nome do grupo de recursos do seu ACR.|
| **Nome de registo\***  | O seu nome de registo ACR. Apenas copie o nome do registo, NÃO o `azurecr.io`nome do servidor de login (por exemplo, sem o .) |
| **Nome repositório\***  | O nome do repositório do seu ACR que contém o seu módulo IoT Edge. **Nota:** Depois do nome ser definido, não pode ser mudado mais tarde. Utilize um nome único para garantir que nenhuma outra oferta na sua conta tem o mesmo nome. |
| **Nome de utilizador\*** | O nome de utilizador associado ao seu ACR (nome de utilizador administrativo). |
| **Senha\*** | A senha associada ao seu ACR. |
|    |  ***Versão da Imagem***   |
| **Etiqueta de imagem ou digestão\*** | Deve pelo menos `latest` incluir uma etiqueta e uma etiqueta `xx.xx.xx-` de versão (por exemplo, a começar pelo número xx). Devem ser [marcas de manifesto](https://github.com/estesp/manifest-tool) para atingir várias plataformas. Todas as etiquetas referenciadas por uma etiqueta de manifesto também devem ser adicionadas para que possamos carregá-las. Pode adicionar várias versões de um módulo IoT Edge utilizando tags. Todas as etiquetas `latest`de manifesto `X.Y-` (exceto) devem começar por ambos ou `X.Y.Z-` onde X, Y, Z são inteiros. Saiba mais sobre [tags e versão em "Prepare os seus ativos técnicos do módulo IoT Edge".](./cpp-create-technical-assets.md) <br/> Por exemplo, `latest` se uma etiqueta `1.0.1-linux-x64`apontar `1.0.1-linux-arm32`para `1.0.1-windows-arm32`isso, e , estas 6 tags precisam de ser adicionadas aqui. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Ajude os seus clientes a lançar usando definições padrão

Defina as definições mais comuns para implementar o módulo IoT Edge. Otimize as implementações do cliente, permitindo-lhes lançar o seu módulo IoT Edge fora da caixa com estes predefinições.

![Definições predefinidas do módulo IoT Edge na implementação](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

A tabela que se segue descreve o propósito, o conteúdo e a formatação dos campos para **Rotas Padrão,** **propriedades padrão duplas desejadas,** variáveis de **ambiente predefinidos**e **Definições de Predefinição**.

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **Rotas predefinidas**        | Cada nome e valor de rota padrão devem ser inferiores a 512 caracteres. Pode definir até 5 rotas predefinidas. Certifique-se de que utiliza uma [sintaxe](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) de rota correta no valor da rota. Para se referir ao seu módulo, utilize o seu nome de módulo predefinido, que será o seu **Título SKU** sem espaços e caracteres especiais. Para se referir a outros módulos `<FROM_MODULE_NAME>` ainda não conhecidos, utilize a convenção para informar os seus clientes de que precisam de atualizar esta informação. Saiba mais sobre [as rotas IoT Edge.](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) <br/> Por exemplo, `ContosoModule` se o módulo ouvir `ContosoInput` as inputs e os dados de `ContosoOutput`saída, faz sentido definir as seguintes 2 rotas predefinidas:<br/>- Nome #1:`ToContosoModule`<br/>- Valor #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- Nome #2:`FromContosoModuleToCloud`<br/>- Valor #2:`FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Propriedades desejadas por padrão**      | Cada nome e valor de propriedades gémeas desejadas por predefinido deve ser inferior a 512 caracteres. Pode definir até 5 propriedades desejadas por nome/valor twin. Os valores das propriedades gémeas desejadas devem ser válidos JSON, não escapados, sem matrizes e com uma hierarquia aninhada máxima de 4. Saiba mais sobre [as propriedades gémeas desejadas.](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties) <br/> Por exemplo, se um módulo suporta uma taxa de atualização dinamicamente configurável através de propriedades duplas desejadas, faz sentido definir a seguinte propriedade padrão twin desejada:<br/> - Nome #1:`RefreshRate`<br/>- Valor #1:`60`|
| **Variáveis ambientais padrão**  | Cada nome e valor das variáveis de ambiente padrão devem ser inferiores a 512 caracteres. Pode definir até 5 variáveis de ambiente de nome/valor. <br/>Por exemplo, se um módulo necessitar de aceitar termos de utilização antes de ser iniciado, pode definir a seguinte variável ambiental:<br/> - Nome #1:`ACCEPT_EULA`<br/>- Valor #1:`Y`|
| **Definição de definição DeOpÇões**  | As opções de criação devem ter menos de 512 caracteres. Deve ser válido JSON, sem fuga. Saiba mais sobre [criaçõesOp](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Por exemplo, se um módulo necessitar de ligar uma porta, pode definir as seguintes opções de criação:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Selecione **Guardar** para guardar as definições de SKU. 


## <a name="next-steps"></a>Passos seguintes

Utilize o [separador Marketplace](./cpp-marketplace-tab.md) para criar uma descrição do mercado para a sua oferta.
