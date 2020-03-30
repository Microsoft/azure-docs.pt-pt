---
title: incluir ficheiro
description: incluir ficheiro
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/13/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: f2446a924ceed37c51779efc9d9e94c0252a2067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80321840"
---
O separador **test drive** permite-lhe configurar uma demonstração (ou "test drive") que permitirá aos clientes experimentar a sua oferta antes de se comprometerem a comprá-la. Saiba mais no artigo [O que é Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) Se já não quiser fornecer um test drive para a sua oferta, volte à página de **configuração da Oferta** e desverifique o **test drive Enable**.

### <a name="technical-configuration"></a>Configuração técnica
Estão disponíveis os seguintes tipos de test drives, cada um com os seus próprios requisitos de configuração técnica.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplicação lógica](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configuração técnica não necessária)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuração técnica para unidade de teste Do Gestor de Recursos Azure

Um modelo de implantação que contém todos os recursos Azure que compõem a sua solução. Os produtos que se adequam a este cenário utilizam apenas recursos Azure. Saiba mais sobre a criação de um [test drive Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiões** (necessárias): Atualmente existem 26 regiões apoiadas pelo Azure onde o seu test drive pode ser disponibilizado. Normalmente, você vai querer disponibilizar o seu test drive nas regiões onde você antecipa o maior número de clientes, para que eles possam selecionar a região mais próxima para o melhor desempenho. Terá de se certificar de que a sua subscrição é autorizada a disponibilizar todos os recursos necessários em cada uma das regiões que está a selecionar.

- **Casos**: Selecione o tipo (quente ou frio) e o número de instâncias disponíveis, que serão multiplicadas pelo número de regiões onde a sua oferta está disponível.

**Quente**: Este tipo de instância é implantado e aguarda acesso por região selecionada. Os clientes podem aceder instantaneamente a casos *quentes* de um test drive, em vez de terem de esperar por uma implementação. A contrapartida é que estes casos estão sempre a decorrer na sua subscrição Azure, pelo que incorrerão num custo de uptime maior. É altamente recomendado ter pelo menos uma instância *Hot,* uma vez que a maioria dos clientes não quer esperar por implementações completas, resultando numa entrega no uso do cliente se não houver uma instância *hot* disponível.

**Frio**: Este tipo de instância representa o número total de casos que podem ser eventualmente implantados por região. As instâncias frias requerem que todo o modelo de Gestor de Recursos test drive seja implantado quando um cliente solicita a unidade de teste, por isso as instâncias *frias* são muito mais lentas a carregar do que as instâncias *hot.* A contrapartida é que só tem de pagar a duração do test drive, *nem* sempre está a funcionar na sua subscrição Azure como com uma instância *Hot.*

- Modelo de gestor de **recursos Azure de tração**de teste: Faça upload do .zip contendo o seu modelo de Gestor de Recursos Azure.  Saiba mais sobre a criação de um modelo de Gestor de Recursos Azure no artigo quickstart Criar e implementar modelos de [Gestor de Recursos Azure utilizando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duração da unidade** de teste (necessária): Introduza o tempo de permanência da Test Drive, em # de horas. O Test Drive termina automaticamente após o fim deste período de tempo. Esta duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válida).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuração técnica para unidade de teste Dynamics 365

A Microsoft pode remover a complexidade da instalação de um test drive hospedando e mantendo o fornecimento e implementação do serviço utilizando este tipo de test drive. A configuração para este tipo de test drive hospedado é a mesma, independentemente de o test drive estar direcionado para um público de Business Central, Customer Engagement ou Operations.

- **Unidades de teste simultâneas max** (necessárias): Detete o número máximo de clientes que podem utilizar o seu test drive de uma só vez. Cada utilizador simultâneo consumirá uma licença Dynamics 365 enquanto o test drive estiver ativo, pelo que terá de garantir que tem licenças suficientes para suportar o limite máximo definido. Valor recomendado de 3-5.

- **Duração** da unidade de teste (necessária): Introduza o tempo de permanência da Unidade de Teste, definindo o número de horas. Após tantas horas, a sessão terminará e deixará de consumir uma das suas licenças. Recomendamos um valor de 2 a 24 horas dependendo da complexidade da sua oferta. Esta duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válida).  O utilizador pode solicitar uma nova sessão se ficar sem tempo e pretender aceder novamente ao test drive.

- **URL de instância** (necessário): O URL onde o cliente iniciará o seu test drive. Tipicamente, o URL da sua instância Dynamics 365 executando `https://testdrive.crm.dynamics.com`a sua aplicação com dados de amostra instalados (por exemplo).

- **Por exemplo Web API URL** (necessário): Recupere o URL Da Web API para a sua instância Dynamics 365, iniciando sessão na sua conta Microsoft 365 e navegando para **Definições** \&gt; **Personalização** \&gt; **Recursos** \&de Desenvolvimento gt; **Instância Web API (URL raiz de serviço)**, copiar `https://testdrive.crm.dynamics.com/api/data/v9.0`o URL encontrado aqui (por exemplo).

- Nome de **função** (obrigatório): Forneça o nome de função de segurança que definiu no seu test drive personalizado Dynamics 365. Isto será atribuído ao utilizador durante o seu test drive (por exemplo, função de test-drive).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuração técnica para unidade de teste de aplicativo lógica

Qualquer produto personalizado deve usar este tipo de modelo de implementação de test drive que engloba uma variedade de arquiteturas de solução complexas. Para mais informações sobre a configuração de unidades de teste de Aplicações Lógicas, visite [Operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Envolvimento do Cliente](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no GitHub.

- **Região** (necessária, lista de abandono de seleção única): Atualmente existem 26 regiões apoiadas pelo Azure onde o seu test drive pode ser disponibilizado. Os recursos para a sua aplicação Logic serão implantados na região que selecionar. Se a sua App Lógica tiver algum recursos personalizados armazenados numa região específica, certifique-se de que a região é selecionada aqui. A melhor maneira de o fazer é implementar totalmente a sua Aplicação Lógica localmente na sua subscrição Azure no portal e verificar se funciona corretamente antes de fazer esta seleção.

- **Unidades de teste simultâneas max** (necessárias): Detete o número máximo de clientes que podem utilizar o seu test drive de uma só vez. Estes test drives já estão implantados, permitindo aos clientes acederem instantaneamente sem esperar por uma implementação.

- **Duração da unidade** de teste (necessária): Introduza o tempo de permanência da Test Drive, em # de horas. O test drive termina automaticamente após o fim deste período de tempo.

- Nome de grupo de **recursos Azure** (necessário): Introduza o nome do grupo de [recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) onde o seu test drive de aplicação lógica é guardado.

- Nome da **aplicação azure logic** (necessário): Introduza o nome da aplicação Logic que atribui o test drive ao utilizador. Esta aplicação Logic deve ser guardada no grupo de recursos Azure acima.

- **Deprovisionamento lógica nome de aplicação** (necessário): Introduza o nome da aplicação Logic que desprovisiona o test drive uma vez que o cliente esteja terminado. Esta aplicação Logic deve ser guardada no grupo de recursos Azure acima.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuração técnica não necessária para unidades de teste Power BI

Os produtos que queiram demonstrar um visual interativo do Power BI podem usar um link incorporado para partilhar um dashboard personalizado como seu test drive, não sendo necessária nenhuma configuração técnica adicional. Saiba mais sobre a configuração de aplicações de modelo[Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Detalhes da subscrição de implementação

Para implementar o Test Drive em seu nome, por favor crie e forneça uma assinatura Azure separada e única. (Não é necessário para as unidades de teste Power BI).

- **ID de subscrição Azure** (necessário para o Gestor de Recursos Azure e aplicações lógicas): Introduza o ID de subscrição para conceder acesso aos serviços da sua conta Azure para relatórios de utilização de recursos e faturação. Recomendamos que considere [criar uma subscrição Azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) para usar para test drives se ainda não tiver uma. Pode encontrar o seu ID de subscrição Azure iniciando sessão no [portal Azure](https://portal.azure.com/) e navegando no separador **Subscrições** do menu do lado esquerdo. A seleção do separador irá exibir o seu ID de subscrição (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID de inquilino Azure AD** (obrigatório): Insira o seu Id de [inquilino](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directy no menu esquerdo, selecione **Properties,** em seguida, procure o número de ID do **Diretório** listado (por exemplo 50c464d3-4930-494c-963c-1e951d15360e). Também pode procurar o ID do inquilino da sua [https://www.whatismytenantid.com](https://www.whatismytenantid.com)organização usando o seu URL de nome de domínio em: .

- Nome de **inquilino Azure AD** (necessário para Dinâmico 365): Insira o nome de Diretório Ativo Azure (AD). Para encontrar este nome, inscreva-se no [portal Azure,](https://portal.azure.com/)no canto superior direito o seu nome de inquilino será listado sob o nome da sua conta.

- **ID da aplicação Azure AD** (obrigatório): Introduza o id da [aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Ative Directory (AD). Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directy no menu esquerdo, selecione registos de **Aplicações,** em seguida, procure o número de ID de **aplicação** listado (por exemplo 50c464d3-4930-494c-963c-1e951d15360e).

- Segredo de cliente da **aplicação Azure AD** (necessário): Insira o [segredo do cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)da aplicação Azure AD . Para encontrar este valor, inscreva-se no [portal Azure.](https://portal.azure.com/) Selecione o separador **Diretório Ativo Azure** no menu esquerdo, selecione **as inscrições**da App e, em seguida, selecione a sua aplicação de test drive. Em seguida, selecione **Certificados e segredos**, selecione **Novo segredo do cliente,** introduza uma descrição, selecione **Never** under **Expires,** em seguida, escolha **Adicionar**. Certifique-se de copiar o valor. (Não navegue para longe da página antes de fazer isto, caso contrário não terá acesso ao valor.)

Lembre-se de **Guardar** antes de passar para a próxima secção!

### <a name="test-drive-listings-optional"></a>Listas de test drive (opcional)

A opção de **listagem Test Drive** encontrada no separador Test **drive** mostra os idiomas (e mercados) onde o seu test drive está disponível, atualmente o inglês (Estados Unidos) é o único local disponível. Além disso, esta página apresenta o estado da listagem específica da língua e a data/hora que foi adicionada. Terá de definir os detalhes do test drive (descrição, manual do utilizador, vídeos, etc.) para cada idioma/mercado.

- **Descrição** (necessária): Descreva o seu test drive, o que será demonstrado, objetivos para o utilizador experimentar, funcionalidades a explorar e qualquer informação relevante para ajudar o utilizador a determinar se adquire a sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo. 

- **Informação de acesso** (necessária para o Gestor de Recursos Azure e unidades de teste lógicas): Explique o que um cliente precisa de saber para aceder e utilizar este test drive. Caminhe por um cenário de utilização da sua oferta e exatamente o que o cliente deve saber para aceder a funcionalidades durante todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do Utilizador** (obrigatório): Uma passagem aprofundada da sua experiência de test drive. O Manual do Utilizador deve cobrir exatamente o que pretende que o cliente ganhe ao experimentar o test drive e servir de referência para quaisquer questões que possam ter. O ficheiro deve estar em formato PDF e ser nomeado (255 caracteres no máximo) após o upload.

- **Vídeos: Adicionar vídeos** (opcional): Os vídeos podem ser enviados para o YouTube ou Vimeo e referenciados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa ver uma caminhada através de informações para ajudá-los a entender melhor o test drive, incluindo como usar com sucesso as funcionalidades da sua oferta e entender cenários que realçam os seus benefícios.
  - **Nome** (obrigatório)
  - **URL (apenas YouTube ou Vimeo) (obrigatório)**
  - **Miniatura (533 x 324px)**: O ficheiro de imagem deve estar no formato PNG.

Selecione **Guardar** depois de completar estes campos.
