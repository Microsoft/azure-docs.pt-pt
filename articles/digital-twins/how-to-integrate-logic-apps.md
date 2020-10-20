---
title: Integrar no Logic Apps
titleSuffix: Azure Digital Twins
description: Veja como ligar apps lógicas a Azure Digital Twins, usando um conector personalizado
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 21e72e63dae2c52d04aca0cd11971fe5cd23fb47
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207551"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Integre com As Aplicações Lógicas usando um conector personalizado

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) é um serviço de cloud que o ajuda a automatizar fluxos de trabalho através de apps e serviços. Ao ligar as Aplicações Lógicas às APIs das Gémeas Digitais Azure, pode criar fluxos automatizados em torno das Gémeas Digitais Azure e dos seus dados.

A Azure Digital Twins não tem atualmente um conector certificado (pré-construído) para aplicações lógicas. Em vez disso, o processo atual de utilização de Apps Lógicas com Gémeos Digitais Azure é criar um [**conector de Aplicações Lógicas personalizado**](../logic-apps/custom-connector-overview.md), utilizando um [Azure Digital Twins Swagger](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) que foi modificado para trabalhar com apps lógicas.

> [!NOTE]
> Existem várias versões do Swagger contidas na amostra personalizada de Swagger ligada acima. A versão mais recente será encontrada na sub-página com a data mais recente, mas as versões anteriores contidas na amostra também são suportadas.

Neste artigo, você usará o [portal Azure](https://portal.azure.com) para **criar um conector personalizado** que pode ser usado para ligar Apps Lógicas a uma instância Azure Digital Twins. Em seguida, **irá criar uma aplicação lógica** que utiliza esta ligação para um cenário de exemplo, no qual os eventos desencadeados por um temporizador irão atualizar automaticamente um gémeo na sua instância Azure Digital Twins. 

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, **crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ** antes de começar.
Inscreva-se no [portal Azure](https://portal.azure.com) com esta conta. 

Também precisa de completar os seguintes itens como parte da configuração pré-requisito. O restante desta secção irá acompanhá-lo através destes passos:
- Configurar um exemplo de Gémeos Digitais Azure
- Obtenha o segredo do cliente de registo de aplicativos
- Adicione um gémeo digital

### <a name="set-up-azure-digital-twins-instance"></a>Configurar a instância Azure Digital Twins

Para ligar uma instância Azure Digital Twins a Logic Apps neste artigo, terá de ter a **instância Azure Digital Twins** já configurada. 

Em primeiro lugar, **crie uma instância Azure Digital Twins** e a autenticação necessária para poder trabalhar com ela. Para isso, siga as instruções em [*Como-a-: Configurar uma instância e autenticação*](how-to-set-up-instance-portal.md). Dependendo da sua experiência preferida, o artigo de configuração é oferecido para o [portal Azure,](how-to-set-up-instance-portal.md) [CLI,](how-to-set-up-instance-cli.md)ou [amostra de script de implementação automatizada da Cloud Shell](how-to-set-up-instance-scripted.md). Todas as versões das instruções também contêm passos para verificar se completou cada passo com sucesso e estão prontos para passar a usar a sua nova instância.
* Depois de configurar o seu exemplo Azure Digital Twins, você precisará do nome de **_anfitrião_** do caso[(encontre no portal Azure).](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Para autenticar a aplicação ADT Explorer, também terá de configurar um **registo de aplicações.** Siga as instruções em [*Como-a: Crie um registo de aplicações*](how-to-create-app-registration.md) para configurar isto. 
* Uma vez que você tem um registro de aplicação, você precisará do ID de **_Aplicação (cliente)_** do registo e **_diretório (inquilino) ID (encontre_** [no portal Azure).](how-to-create-app-registration.md#collect-client-id-and-tenant-id)

### <a name="get-app-registration-client-secret"></a>Obtenha o segredo do cliente de registo de aplicativos

Também terá de criar um segredo de **_Cliente_** para o seu registo de aplicações AZure AD. Para isso, navegue para a página [de registos](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App no portal Azure (pode utilizar este link ou procurá-lo na barra de pesquisa do portal). Selecione a sua inscrição que criou na secção anterior da lista, de forma a abrir os seus detalhes. 

Bata *certificados e segredos* no menu do registo e selecione *+ Novo segredo do cliente.*

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

Introduza os valores que quiser para Descrição e Expira, e bata *Em Adicionar*.

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

Agora, verifique se o segredo do cliente está visível na página _de segredos & certificados_ com campos _expira_ e _valor._ Tome nota do seu _Valor_ para usar mais tarde (também pode copiá-lo para a área de transferência com o ícone Copy)

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

### <a name="add-a-digital-twin"></a>Adicione um gémeo digital

Este artigo utiliza Apps Lógicas para atualizar um gémeo na sua instância Azure Digital Twins. Para prosseguir, deve adicionar pelo menos um gémeo no seu caso. 

Pode adicionar gémeos utilizando as [APIs DigitalTwins,](how-to-use-apis-sdks.md)a [.NET (C#) SDK,](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)ou o [Azure Digital Twins CLI](how-to-use-cli.md). Para obter passos detalhados sobre como criar gémeos utilizando estes métodos, consulte [*Como-a-fazer: Gerir gémeos digitais*](how-to-manage-twin.md).

Vai precisar da **_identificação de_** um gémeo no seu caso que criou.

## <a name="create-custom-logic-apps-connector"></a>Crie o conector de Apps Lógicas personalizadas

Neste passo, irá criar um [conector de Apps Lógicas personalizado](../logic-apps/custom-connector-overview.md) para as APIs de Gémeos Digitais Azure. Depois de o fazer, poderá ligar a Azure Digital Twins ao criar uma aplicação lógica na secção seguinte.

Navegue para a página [de Connector Personalizado de Aplicações Lógicas](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) no portal Azure (pode utilizar este link ou procurá-lo na barra de pesquisa do portal). Hit *+ Add*.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

Na página *do Conector Personalizado create Logic Apps* que se segue, selecione o seu grupo de subscrição e recursos e um nome e localização de implementação para o seu novo conector. Hit *Review + criar*. 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

Isto irá levá-lo ao separador *'Rever +' criar,* onde pode *acertar* criar na parte inferior para criar o seu recurso.

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

Serão levados para a página de implantação do conector. Quando terminar a implementação, premir o botão *Go para o recurso* para visualizar os detalhes do conector no portal.

### <a name="configure-connector-for-azure-digital-twins"></a>Conector configurar para gémeos digitais Azure

Em seguida, configurará o conector que criou para chegar às Gémeas Digitais Azure.

Primeiro, faça o download de um Azure Digital Twins Swagger que foi modificado para trabalhar com as Aplicações Lógicas. Descarregue a amostra **personalizada da Azure Digital Twins (Connector De Aplicações Lógicas)** a partir [**deste link,**](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) premindo o botão *Download ZIP.* Navegue para a pasta *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* descarregada e desaperte-a. 

O Swagger personalizado para este tutorial está localizado na pasta _**Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps **_. Esta pasta contém sub-dobradeiras chamadas *estáveis* e *de pré-visualização,* ambas com diferentes versões do Swagger organizadas por data. A pasta com a data mais recente conterá a mais recente cópia do Swagger. Seja qual for a versão que selecionar, o ficheiro Swagger é nomeado _** digitaltwins.jsem**_.

> [!NOTE]
> A menos que esteja a trabalhar com uma funcionalidade de pré-visualização, é geralmente recomendado usar a versão mais recente *e estável* do Swagger. No entanto, versões anteriores e versões de pré-visualização do Swagger também são suportadas. 

Em seguida, vá à página geral do seu conector no [portal Azure](https://portal.azure.com) e bata *em Edit*.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

Na página de *Conector Personalizado de Aplicações Lógicas* de Edição que se segue, configuure estas informações:
* **Conectores personalizados**
    - Ponto final da API: REST (padrão de licença)
    - Modo de importação: Ficheiro OpenAPI (padrão de licença)
    - Arquivo: Este será o ficheiro Swagger personalizado que descarregou anteriormente. Hit *Import*, localizar o ficheiro na sua máquina *(Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps \...\digitaltwins.jsligados*) e acertar Em *Open*.
* **Informação geral**
    - Ícone: Faça upload de um ícone que você gosta
    - Cor de fundo do ícone: Introduza o código hexadecimal no formato '#xxxxxx' para a sua cor.
    - Descrição: Preencha os valores que quiser.
    - Regime: HTTPS (incumprimento de licença)
    - Anfitrião: O *nome de anfitrião* da sua instância Azure Digital Twins.
    - URL de base: / (deixar padrão)

Em seguida, adiem o botão *de Segurança* na parte inferior da janela para continuar até ao próximo passo de configuração.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

Na etapa de Segurança, *acerte editar* e configuure esta informação:
* **Tipo de autenticação**: Oauth 2.0
* **OAuth 2.0**:
    - Fornecedor de identidade: Azure Ative Directory
    - ID do cliente: O *ID de aplicação (cliente)* para o seu registo de aplicações AZure AD
    - Segredo do cliente: O *segredo do Cliente* que criou em [*Pré-requisitos*](#prerequisites) para o seu registo de aplicações AD Azure
    - URL de login: https://login.windows.net (deixar padrão)
    - ID do inquilino: O *ID do Diretório (inquilino)* para o seu registo de aplicações AZure AD
    - URL de recurso: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Âmbito: Diretório.AccessAsUser.All
    - Url de redirecionamento: (deixar por defeito por enquanto)

Note que o campo URL de redirecionamento diz *Salvar o conector personalizado para gerar o URL de redirecionamento*. Faça isto agora, atingindo o *conector Update* através da parte superior do painel para confirmar as definições do conector.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

<!-- Success message? didn't see one -->

Volte ao campo URL de redirecionamento e copie o valor que foi gerado. Vais usá-lo no próximo passo.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

Esta é toda a informação necessária para criar o seu conector (não há necessidade de continuar a ultrapassar a Segurança até ao passo Definição). Pode fechar o painel de *conector personalizado de aplicações lógicas de edição.*

>[!NOTE]
>De volta à página geral do seu conector onde inicialmente atingiu *Edit*, note que bater *novamente* em Edit irá reiniciar todo o processo de inserimento das suas escolhas de configuração. Não irá povoar os seus valores da última vez que passou por ele, por isso, se quiser guardar uma configuração atualizada com quaisquer valores alterados, deve reintroduminar todos os outros valores também para evitar que sejam substituídos por defeitos.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Conceder permissões de conector na app AZure AD

Em seguida, utilize o valor *URL de redirecionamento* do conector personalizado que copiou no último passo para conceder as permissões do conector no seu registo de aplicações AD Azure.

Navegue na página [de inscrições](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App no portal Azure e selecione o seu registo na lista. 

Em *Autenticação* do menu do registo, adicione um URI.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'"::: 

Introduza o URL *de redirecionamento* do conector personalizado para o novo campo e atinja o ícone *Save.*

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

Já terminou a configuração de um conector personalizado que pode aceder às APIs das Gémeas Digitais Azure. 

## <a name="create-logic-app"></a>Criar uma aplicação lógica

Em seguida, irá criar uma aplicação lógica que utilizará o seu novo conector para automatizar as atualizações da Azure Digital Twins.

No [portal Azure,](https://portal.azure.com)procure *aplicações Lógicas* na barra de pesquisa do portal. Selecioná-lo deve levá-lo para a página *de aplicações Lógica.* Acerte no botão *de aplicação lógica Create* para criar uma nova aplicação lógica.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

Na página *Da Aplicação Lógica* que se segue, insira o seu grupo de subscrição e recursos. Além disso, escolha um nome para a sua aplicação lógica e selecione a localização da implementação.

Carregar no botão _Review + criar._

Isto irá levá-lo ao separador *'Rever +' criar,* onde pode rever os seus dados e *acertar* criar na parte inferior para criar o seu recurso.

Você será levado para a página de implementação para a aplicação lógica. Quando terminar a implementação, toque no botão *Go para o recurso* para continuar no Logic Apps *Designer,* onde preencherá a lógica do fluxo de trabalho.

### <a name="design-workflow"></a>Fluxo de trabalho de design

No *Logic Apps Designer*, em *Iniciar com um gatilho comum,* selecione _**Recorrência**_.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

Na página *De Design de Aplicações Lógicas* que se segue, altere a Frequência **de Recorrência** para *Segundo*, para que o evento seja desencadeado a cada 3 segundos. Isto facilitará a início dos resultados sem ter de esperar muito tempo.

Hit *+ Novo passo*.

Isto abrirá uma caixa *de ação para escolher.* Mude para o *separador Personalizado.* Deve ver o seu conector personalizado mais cedo na caixa de cima.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

Selecione-o para visualizar a lista de APIs contidas no conector. Utilize a barra de pesquisa ou percorra a lista para selecionar **DigitalTwins_Add**. (Esta é a API utilizada neste artigo, mas também pode selecionar qualquer outra API como uma escolha válida para uma ligação de Apps Lógicas).

Pode ser-lhe pedido que faça sedús com as suas credenciais Azure para se ligar ao conector. Se tiver um *diálogo solicitado permissões,* siga as instruções para conceder o consentimento para a sua app e aceite.

Na nova caixa *DigitalTwinsAdd,* preencha os campos da seguinte forma:
* _id_: Preencha o *ID Twin* do gémeo digital no seu caso que gostaria que a App Lógica atualizasse.
* _twin_: Este campo é onde você vai entrar no corpo que o pedido de API escolhido requer. Para *o DigitalTwinsUpdate,* este corpo está na forma do código JSON Patch. Para saber mais sobre a estruturação de um JSON Patch para atualizar o seu gémeo, consulte a [Atualização de uma](how-to-manage-twin.md#update-a-digital-twin) secção digital twin de *How-to: Manage digital twins*.
* _versão api_: A versão API mais recente. Na pré-visualização pública atual, este valor é *2020-05-31-pré-visualização*

Hit *Save* in the Logic Apps Designer.

Pode escolher outras operações selecionando _+ Novo passo_ na mesma janela.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Vista do portal de um registo de aplicações AD Azure. Há um destaque em torno de 'Certificados e segredos' no menu de recursos, e um destaque na página em torno de 'Novo segredo de cliente'":::

## <a name="query-twin-to-see-the-update"></a>Consulta twin para ver a atualização

Agora que a sua aplicação lógica foi criada, o evento de atualização dupla que definiu no Logic Apps Designer deve ocorrer numa recorrência a cada três segundos. Isto significa que, após três segundos, deverá ser capaz de consultar o seu gémeo e ver os seus novos valores remendados refletidos.

Pode consultar o seu gémeo através do seu método de escolha (como uma [aplicação personalizada](tutorial-command-line-app.md)para clientes, a [aplicação de amostras Azure Digital Twins Explorer,](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)os [SDKs e APIs,](how-to-use-apis-sdks.md)ou o [CLI).](how-to-use-cli.md) 

Para saber mais sobre a consulta do seu exemplo de Gémeos Digitais Azure, consulte [*Como fazer: Consultar o gráfico gémeo*](how-to-query-graph.md).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou uma aplicação lógica que atualiza regularmente um gémeo na sua instância Azure Digital Twins com um patch que forneceu. Pode experimentar a seleção de outras APIs no conector personalizado para criar Aplicações Lógicas para uma variedade de ações no seu caso.

Para ler mais sobre as operações de APIs disponíveis e os detalhes que necessitam, visite [*Como-a- Use as APIs e SDKs de Gémeos Digitais Azure*](how-to-use-apis-sdks.md).