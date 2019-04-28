---
title: Módulo do Azure IoT Edge SKUs | Documentos da Microsoft
description: Crie SKUs para o módulo do IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 370d8160661c1f73124151a3a49d0bb3170dfb77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60910991"
---
# <a name="iot-edge-module-skus-tab"></a>Separador de SKUs de módulo do IoT Edge

O **SKUs** separador da **nova oferta** página permite-lhe criar um ou mais SKUs e associá-las a sua nova oferta.  Pode usar diferentes SKUs para diferenciar uma solução por conjuntos de funcionalidades, modelos de faturação ou alguns outra característica.

## <a name="sku-settings"></a>Definições de SKU

Quando começar a criar uma nova oferta, não há qualquer SKUs associadas com a oferta. Para criar um novo SKU, siga estes passos:

- Sobre o **módulos do IoT Edge > nova oferta** página, selecione a **SKUs** separador.
- Em SKUs, selecione **+ novo SKU** para abrir uma caixa de diálogo.

  ![Novo botão SKU no separador nova oferta de módulos do IoT Edge](./media/iot-edge-module-skus-tab-new-sku.png)

- Sobre o **novo SKU** caixa de diálogo caixa, introduza um identificador para o SKU e, em seguida, selecione **OK**.
(A tabela seguinte fornece as convenções de nomenclatura do identificador.)

O **SKUs** separador é atualizado e exibe os campos que editar para configurar o SKU. Um asterisco (*) acrescentado ao nome do campo indica que é obrigatório.

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID DO SKU**       | Identificador para este SKU. Este nome tem um máximo de 50 caracteres, consistindo em minúsculas, carateres alfanuméricos ou traços (-), mas não pode terminar com um traço. **Nota:** Não é possível alterar este nome após a oferta de publicação do. O nome é publicamente visível em URLs de produto. |

## <a name="sku-details"></a>Detalhes SKU

Configurar o **detalhes de SKU** para definir como será apresentado o SKU dos sites do Azure Marketplace e o Portal do Azure.

![Metadados de sku de módulo do IoT Edge](media/iot-edge-module-skus-tab-metadata.png)

A tabela seguinte descreve a finalidade, conteúda e formatação para os campos no **detalhes de SKU**.

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **Title** (Título)        | Título para este SKU. Comprimento máximo de 50 carateres. <br/> Ele será apresentado no Portal do Azure e será utilizado como um nome de módulo padrão (sem espaços e carateres especiais) quando for implementada. Ver as imagens abaixo para ver exatamente onde é apresentado neste campo.|
| **Resumo**      | Breve resumo centrado este SKU. Comprimento máximo de 100 carateres. Fazer **não** resumir a oferta, apenas o SKU.  Este resumo será apresentado no Azure Marketplace. Ver as imagens abaixo para ver exatamente onde é apresentado neste campo.|
| **Descrição**  | Breve descrição deste SKU. Comprimento máximo de 3000 carateres. NÃO descrevem a oferta, mas apenas este SKU. Será apresentado no azure marketplace e no portal do Azure. No portal do Azure, serão acrescentada a descrição do Marketplace que descreve a oferta definida no separador Marketplace.  Pode ser o mesmo que o resumo de SKU. Ver as imagens abaixo para ver exatamente onde é apresentado neste campo.|
| **Ocultar este SKU** | Mantenha a configuração padrão, o que é **não**. |

### <a name="sku-example"></a>Exemplo SKU

 Os exemplos seguintes mostram como o SKU **Title**, **resumo**, e **Descrição** campos são apresentados em modos de exibição diferentes.
 
#### <a name="on-the-azure-marketplace-website"></a>No site do Azure Marketplace:

- Ao examinar os detalhes SKU:

    ![Como os SKUs são apresentados no Web site do Azure Marketplace](media/iot-edge-module-ampdotcom-pdp-plans.png)

#### <a name="on-the-azure-portal-website"></a>No site do Portal do Azure:

- Ao navegar em SKUs:

    ![Como módulo do IoT Edge exibido quando navegar no portal do Azure #1](media/iot-edge-module-portal-browse.png)

    ![Como módulo do IoT Edge exibido quando navegar no portal do Azure #2](media/iot-edge-module-portal-product-picker.png)

- Ao pesquisar SKUs:

    ![Como módulo do IoT Edge aparece ao pesquisar o portal do Azure](media/iot-edge-module-portal-search.png)

- Ao examinar os detalhes SKU:

    ![Como módulo do IoT Edge aparece ao ver os detalhes do produto no portal](./media/iot-edge-module-portal-pdp.png)

- Ao implantar o módulo:
    
    ![Como módulo do IoT Edge aparece quando a ser implementada](./media/iot-edge-module-deployment.png)

## <a name="sku-content"></a>Conteúdo do SKU

Sob **imagens de módulo Edge**, forneça as informações necessárias carregar o módulo do IoT Edge.

Envie-no acesso ao seu [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) que contém o módulo de imagem, para que possamos carregá-lo e certificá-lo do IoT Edge. Após a publicação, o módulo do IoT Edge será copiado e distribuídos a utilizar um registo de contentor público alojado pelo Azure Marketplace.

Pode selecionar várias plataformas e fornecer várias versões por meio de marcas. Saiba mais sobre [etiquetas e controle de versão em "Preparar o IoT Edge ativos técnicos do módulo"](./cpp-create-technical-assets.md).

![Imagens de módulo do IoT Edge](./media/iot-edge-module-skus-tab-acr.png)

A tabela seguinte descreve a finalidade, conteúdo e formatação de campos para:

- **Detalhes do repositório de imagem**
- **Versão da imagem**

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
|  ***Detalhes do repositório de imagem***   |  |
| **ID de subscrição**        | O ID de subscrição do Azure do seu ACR.|
| **Nome do grupo de recursos**      | O nome do grupo de recursos de sua ACR.|
| **Nome do registo**  | Nome do seu registo ACR. Apenas copie o nome do registo, não o nome do servidor de início de sessão (por exemplo, sem o `azurecr.io`.) |
| **Nome do repositório**  | O nome do repositório do seu ACR que contém o módulo do IoT Edge. **Nota:** O nome está definido, ele não pode ser alterado mais tarde. Utilize um nome exclusivo para garantir que nenhuma outra oferta na sua conta tem o mesmo nome. |
| **Nome de Utilizador** | O nome de utilizador associada com o ACR (nome de utilizador do administrador). |
| **Palavra-passe** | A palavra-passe associada com o ACR. |
|  ***Versão da imagem***   |  |
| **Resumo ou etiquetas da imagem** | Tem de incluir, pelo menos, um `latest` etiqueta e uma etiqueta de versão (por exemplo, começando com `xx.xx.xx-` onde xx é um número). Eles devem ser [etiquetas de manifesto](https://github.com/estesp/manifest-tool) para várias plataformas de destino. Também devem ser adicionadas a todas as etiquetas referenciadas por uma marca de manifesto, de modo que pode carregá-los. Pode adicionar várias versões do módulo do IoT Edge utilizando etiquetas. Todas as etiquetas de manifesto (exceto `latest`) tem de começar com um `X.Y-` ou `X.Y.Z-` em que X, Y, Z são números inteiros. Saiba mais sobre [etiquetas e controle de versão em "Preparar o IoT Edge ativos técnicos do módulo"](./cpp-create-technical-assets.md). <br/> Por exemplo, se um `latest` Etiquetar pontos para a qual aponta `1.0.1-linux-x64`, `1.0.1-linux-arm32`, e `1.0.1-windows-arm32`, estes 6 etiquetas tem de ser adicionada aqui. |

### <a name="help-your-customers-launch-your-iot-edge-module-by-using-default-settings"></a>Ajudar os seus clientes, inicie o módulo do IoT Edge, utilizando as predefinições

Defina as configurações mais comuns para implantar o módulo do IoT Edge. Otimize as implementações de cliente, permitindo-lhes a iniciar o IoT Edge módulo-de-pronta com esses padrões.

![Definições da predefinição do módulo do IoT Edge na implementação](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

A tabela seguinte descreve a finalidade, conteúdo e formatação de campos para os **rotas predefinidas**, **propriedades pretendidas do duplo de predefinição**, **predefinido variáveis de ambiente**, e **predefinido CreateOptions**.

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **Rotas predefinidas**        | Cada nome da rota predefinida e o valor tem de ter menos de 512 carateres. Pode definir rotas predefinidas que até 5. Certifique-se de usar um correto [encaminhar sintaxe](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) no seu valor de rota. Para fazer referência a seu módulo, utilize o seu nome de módulo padrão, que será sua **título do SKU** sem espaços e carateres especiais. Para fazer referência a outros módulos ainda não conhecidos, utilize o `<FROM_MODULE_NAME>` Convenção para permitir que os seus clientes saibam que têm de atualizar estas informações. Saiba mais sobre [encaminha o IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Por exemplo, se módulo `ContosoModule` escuta para entradas `ContosoInput` e a saída de dados em `ContosoOutput`, faz sentido definir as rotas 2 padrão seguintes:<br/>-Nome #1: `ToContosoModule`<br/>-Valor #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>-Nome #2: `FromContosoModuleToCloud`<br/>-Valor #2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Propriedades pretendida do duplo do padrão**      | Cada nome de propriedades pretendidas do duplo predefinido e o valor tem de ter menos de 512 carateres. Pode definir as propriedades pretendida do duplo do nome/valor até 5. Valores de propriedades de twin pretendido tem de ser um JSON válido, não escape, sem matrizes e com uma hierarquia aninhada máxima de 4. Saiba mais sobre [propriedades pretendidas do duplo](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Por exemplo, se um módulo de oferecer suporte a uma taxa de atualização configuráveis dinamicamente através de propriedades de twin assim o desejar, faz sentido para definir a propriedade de duplo pretendido padrão seguinte:<br/> -Nome #1: `RefreshRate`<br/>-Valor #1: `60`|
| **Variáveis de ambiente de predefinição**  | Cada nome de variáveis de ambiente predefinido e o valor tem de ter menos de 512 carateres. Pode definir variáveis de ambiente de nome/valor até 5. <br/>Por exemplo, se precisar de um módulo para aceitar os termos de utilização antes de a ser iniciada, pode definir a variável de ambiente seguintes:<br/> -Nome #1: `ACCEPT_EULA`<br/>-Valor #1: `Y`|
| **CreateOptions padrão**  | O createOptions tem de ter menos de 512 carateres. Tem de ser um JSON válido, não caracteres de escape. Saiba mais sobre [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Por exemplo, se precisar de um módulo de ligar uma porta, pode definir o createOptions seguintes:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|

<br/> Selecione **guardar** para guardar as definições de SKU. 

## <a name="next-steps"></a>Passos Seguintes

Utilize o [separador Marketplace](./cpp-marketplace-tab.md) para criar uma descrição do marketplace para a sua oferta.
