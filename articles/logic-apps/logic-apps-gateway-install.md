---
title: Instalar um gateway de dados no local
description: Antes de poder aceder aos dados nas instalações a partir de Azure Logic Apps, faça o download e instale o portal de dados no local
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 4b2559ad20036870c6df5c0662bb973f35155bfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576803"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalar o gateway de dados no local para o Azure Logic Apps

Antes de [poder ligar-se a fontes de dados no local a partir de Azure Logic Apps,](../logic-apps/logic-apps-gateway-connection.md)descarregue e instale o [portal de dados no local](https://aka.ms/on-premises-data-gateway-installer) num computador local. O gateway funciona como uma ponte que proporciona encriptação e transferência de dados rápidas entre as origens de dados no local e as aplicações lógicas. Pode utilizar a mesma instalação de gateway com outros serviços em nuvem, tais como Power Automamate, Power BI, Power Apps e Azure Analysis Services. Para obter informações sobre como utilizar o portal com estes serviços, consulte estes artigos:

* [Microsoft Power Automatizar porta de dados no local](/power-automate/gateway-reference)
* [Porta de dados do Microsoft Power BI no local](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps no local gateway de dados](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services on-in-local data gateway](../analysis-services/analysis-services-gateway.md)

Este artigo mostra como descarregar, instalar e configurar o seu portal de dados no local para que possa aceder a fontes de dados no local a partir de Azure Logic Apps. Também pode saber mais sobre [como o portal de dados funciona](#gateway-cloud-service) mais tarde neste tópico. Para mais informações sobre o portal, veja [o que é um portal no local?](/data-integration/gateway/service-gateway-onprem) Para automatizar tarefas de instalação e gestão de gateways, visite a galeria PowerShell para os [cmdlets DataGateway PowerShell](https://www.powershellgallery.com/packages/DataGateway/3000.15.15).

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma conta Azure com uma subscrição, [inscreva-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

  * A sua conta Azure tem de ser uma conta de trabalho ou uma conta escolar, que `username@contoso.com` parece. Não pode utilizar contas Azure B2B (convidados) ou contas pessoais da Microsoft, tais como @hotmail.com @outlook.com .

    > [!NOTE]
    > Se se inscreveu para uma oferta microsoft 365 e não forneceu o seu endereço de e-mail de trabalho, o seu endereço pode parecer `username@domain.onmicrosoft.com` . Sua conta está guardada num inquilino da AD Azure. Na maioria dos casos, o Nome Principal do Utilizador (UPN) para a sua conta Azure é o mesmo que o seu endereço de e-mail.

    Para utilizar uma [subscrição Visual Studio Standard](https://visualstudio.microsoft.com/vs/pricing/) que esteja associada a uma conta Microsoft, [primeiro crie um inquilino AD Azure](../active-directory/develop/quickstart-create-new-tenant.md) ou use o diretório predefinido. Adicione um utilizador com uma palavra-passe ao diretório e, em seguida, dê a esse utilizador acesso à sua subscrição Azure. Em seguida, pode iniciar seduca durante a instalação gateway com este nome de utilizador e senha.

  * A sua conta Azure deve pertencer apenas a um único [inquilino ou diretório Azure Ative Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology) Tem de utilizar a mesma conta Azure para instalar e administrar o portal no seu computador local.

  * Quando instalar o gateway, iniciará seduca com a sua conta Azure, que liga a sua instalação gateway à sua conta Azure e apenas a essa conta. Não é possível ligar a mesma instalação de gateway através de várias contas Azure ou inquilinos da AD Azure.

  * Mais tarde, no portal Azure, é necessário utilizar a mesma conta Azure para criar um recurso de gateway Azure que se ligue à instalação do gateway. Pode ligar apenas uma instalação de gateway e um recurso de gateway Azure entre si. No entanto, a sua conta Azure pode ligar-se a diferentes instalações de gateway que estão associadas a um recurso de gateway Azure. As suas aplicações lógicas podem então usar este recurso gateway em gatilhos e ações que podem aceder a fontes de dados no local.

* Aqui estão os requisitos para o seu computador local:

  **Requisitos mínimos**

  * .Net Framework 4.7.2
  * Versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

  **Requisitos recomendados**

  * CPU de 8 núcleos
  * Memória de 8 GB
  * Versão de 64 bits do Windows Server 2012 R2 ou mais tarde
  * Armazenamento de unidade de estado sólido (SSD) para bobines

  > [!NOTE]
  > O gateway não suporta o Núcleo do Servidor do Windows.

* **Considerações relacionadas**

  * Instale o portal de dados no local apenas num computador local, e não num controlador de domínio. Não é preciso instalar o portal no mesmo computador que a sua fonte de dados. Precisa apenas de um portal para todas as suas fontes de dados, por isso não precisa de instalar o portal para cada fonte de dados.

    > [!TIP]
    > Para minimizar a latência, pode instalar o gateway o mais próximo possível da sua fonte de dados, ou no mesmo computador, assumindo que tem permissões.

  * Instale o portal num computador local que esteja numa rede com fios, ligado à internet, sempre ligado e não durma. Caso contrário, o portal não pode funcionar, e o desempenho pode sofrer por uma rede sem fios.

  * Se pretender utilizar a autenticação do Windows, certifique-se de que instala o gateway num computador que é membro do mesmo ambiente ative directory que as suas fontes de dados.

  * A região que seleciona para a instalação do gateway é o mesmo local que deve selecionar quando mais tarde criar o recurso de gateway Azure para a sua aplicação lógica. Por padrão, esta região é a mesma localização que o seu inquilino AZure AD que gere a sua conta de utilizador Azure. No entanto, pode alterar a localização durante a instalação do gateway ou mais tarde.

    > [!IMPORTANT]
    > Durante a configuração do gateway, o comando **change region** está indisponível se você assinar com a sua conta do Governo Azure, que está associado a um inquilino do Azure Ative Directory (Azure AD) na nuvem do [Governo Azure.](../azure-government/compare-azure-government-global-azure.md) O gateway utiliza automaticamente a mesma região que o inquilino AZure AD da sua conta de utilizador.
    > 
    > Para continuar a usar a sua conta do Governo Azure, mas crie a porta de entrada para trabalhar na nuvem global multi-arrendatário Azure Commercial, insinuem-se pela primeira vez durante a instalação gateway com o `prod@microsoft.com` nome de utilizador. Esta solução força a porta de entrada para usar a nuvem global de multi-inquilinos Azure, mas ainda assim permite que você continue usando sua conta do Governo Azure.

  * Se estiver a atualizar a instalação do gateway, desinstale primeiro o seu gateway atual para uma experiência mais limpa.

    Como uma boa prática, certifique-se de que está a usar uma versão suportada. A Microsoft lança uma nova atualização para o portal de dados no local todos os meses, e atualmente suporta apenas as últimas seis versões para o portal de dados no local. Se tiver problemas com a versão que está a utilizar, tente [atualizar para a versão mais recente,](https://aka.ms/on-premises-data-gateway-installer) pois o seu problema pode ser resolvido na versão mais recente.

  * O gateway tem dois modos: modo padrão e modo pessoal, que se aplica apenas ao Power BI. Não se pode ter mais do que um portal a funcionar no mesmo modo no mesmo computador.

  * A Azure Logic Apps suporta operações de leitura e escrita através do gateway. No entanto, estas operações têm [limites no seu tamanho de carga](/data-integration/gateway/service-gateway-onprem#considerations)útil.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalar o gateway de dados

1. [Descarregue e execute o instalador gateway num computador local](https://aka.ms/on-premises-data-gateway-installer).

1. Reveja os requisitos mínimos, mantenha o caminho de instalação predefinido, aceite os termos de utilização e, em seguida, **selecione Instalar**.

   ![Rever os requisitos e aceitar termos de utilização](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Depois de o gateway instalar com sucesso, forneça o endereço de e-mail para a sua conta Azure e, em seguida, selecione **Iniciar sôm,** por exemplo:

   ![Inscreva-se com trabalho ou conta escolar](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   A instalação do gateway pode ligar-se a apenas uma conta Azure.

1. **Selecione Registar uma nova porta de entrada neste computador** Em  >  **seguida**. Este passo regista a sua instalação gateway com o [serviço de nuvem gateway](#gateway-cloud-service).

   ![Registar porta de entrada no computador local](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Forneça estas informações para a sua instalação gateway:

   * Um nome de porta de entrada que é único em todo o seu inquilino AZure AD
   * A chave de recuperação, que deve ter pelo menos oito caracteres, que você quer usar
   * Confirmação para a sua chave de recuperação

   ![Fornecer informações para a instalação do gateway](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Guarde e guarde a chave de recuperação num local seguro. Você precisa desta chave se você quiser mudar o local, mover, recuperar ou assumir uma instalação de gateway.

   Note a opção de **Adicionar a um cluster de gateway existente,** que seleciona quando instala portais adicionais para [cenários de alta disponibilidade](#high-availability).

1. Consulte a região para ver o serviço de cloud gateway e a instância de mensagens de autocarro do [Serviço Azure](../service-bus-messaging/service-bus-messaging-overview.md) que é usada pela sua instalação gateway. Por padrão, esta região é a mesma localização que o inquilino AD AZure para a sua conta Azure.

   ![Confirme a região para o serviço de gateway e ônibus de serviço](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Para aceitar a região predefinida, selecione **Configure**. No entanto, se a região padrão não for a que está mais próxima, pode mudar a região.

   *Porquê mudar a região para a sua instalação de gateway?*

   Por exemplo, para reduzir a latência, pode mudar a região do seu gateway para a mesma região que a sua aplicação lógica. Ou, pode selecionar a região mais próxima da sua fonte de dados no local. O seu *recurso gateway em Azure* e a sua aplicação lógica podem ter diferentes localizações.

   1. Junto à região atual, selecione **Região de Mudança.**

      ![Alterar a atual região de gateway](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Na página seguinte, abra a lista **'Região Seletiva',** selecione a região desejada e selecione **'Fazer'**

      ![Selecione outra região para o serviço gateway](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Reveja a informação na janela de confirmação final. Este exemplo utiliza a mesma conta para Apps Lógicas, Power BI, Power Apps e Power Automamate, pelo que o gateway está disponível para todos estes serviços. Quando estiver pronto, **selecione Close**.

   ![Confirmar informações sobre gateway de dados](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Agora [crie o recurso Azure para a sua instalação gateway](../logic-apps/logic-apps-gateway-connection.md).

<a name="communication-settings"></a>

## <a name="check-or-adjust-communication-settings"></a>Verifique ou ajuste as definições de comunicação

O gateway de dados no local depende da [Azure Service Bus Messaging](../service-bus-messaging/service-bus-messaging-overview.md) para a conectividade na nuvem e estabelece as conexões de saída correspondentes à região de Azure associada do gateway. Se o seu ambiente de trabalho exigir que o tráfego passe por um proxy ou firewall para aceder à internet, esta restrição pode impedir que o portal de dados no local se conecte ao serviço de cloud gateway e a Azure Service Bus Messaging. O gateway tem várias definições de comunicação, que pode ajustar.

Um cenário exemplo é o de utilizar conectores personalizados que acedem aos recursos no local utilizando o recurso de gateway de dados no local em Azure. Se também tiver uma firewall que limite o tráfego a endereços IP específicos, tem de configurar a instalação gateway para permitir o acesso aos *[respetivos endereços IP](logic-apps-limits-and-config.md#outbound)geridos geridos.* *Todas as* aplicações lógicas da mesma região utilizam os mesmos intervalos de endereços IP.

Para obter mais informações, veja estes tópicos:

* [Ajustar as definições de comunicação do gateway de dados no local](/data-integration/gateway/service-gateway-communication)
* [Configurar as definições de proxy do gateway de dados no local](/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Suporte de alta disponibilidade

Para evitar pontos únicos de falha no acesso a dados no local, pode ter várias instalações de gateway (apenas modo padrão) com cada um num computador diferente, e confiá-las como um cluster ou grupo. Assim, se o gateway principal não estiver disponível, os pedidos de dados são encaminhados para o segundo portal, e assim por diante. Como pode instalar apenas um portal padrão num computador, tem de instalar cada porta de entrada adicional que está no cluster num computador diferente. Todos os conectores que trabalham com o portal de dados no local suportam alta disponibilidade.

* Já deve ter pelo menos uma instalação de gateway com a mesma conta Azure que o gateway principal e a chave de recuperação para essa instalação.

* O seu gateway principal deve estar a executar a atualização do gateway a partir de novembro de 2017 ou mais tarde.

Depois de configurar o seu gateway principal, quando for instalar outro gateway, **selecione Adicione a um cluster de gateway existente,** selecione o gateway principal, que é o primeiro portal que instalou, e forneça a chave de recuperação para esse gateway. Para obter mais informações, consulte [clusters de alta disponibilidade para o gateway de dados no local.](/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Alterar localização, migrar, restaurar ou assumir o portal existente

Se tiver de alterar a localização do seu gateway, mover a instalação do gateway para um novo computador, recuperar um gateway danificado ou tomar posse de um gateway existente, precisa da chave de recuperação fornecida durante a instalação do gateway.

> [!NOTE]
> Antes de restaurar o gateway no computador que tem a instalação original do gateway, tem primeiro de desinstalar o gateway nesse computador. Esta ação desliga o portal original.
> Se remover ou eliminar um cluster de gateway para qualquer serviço na nuvem, não poderá restaurar esse cluster.

1. Executar o instalador de gateway no computador que tem o gateway existente.

1. Depois de o instalador abrir, inscreva-se com a mesma conta Azure que foi utilizada para instalar o gateway.

1. **Selecione Migrar, restaurar ou adquirir um gateway existente** Em  >  **seguida,** por exemplo:

   ![Selecione "Migrar, restaurar ou adquirir uma porta de entrada existente"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Selecione a partir dos clusters e gateways disponíveis e introduza a chave de recuperação para o gateway selecionado, por exemplo:

   ![Selecione gateway e forneça a chave de recuperação](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Para mudar a região, selecione **Change Region**, e selecione a nova região.

1. Quando estiver pronto, selecione **Configure** para que possa terminar a sua tarefa.

## <a name="tenant-level-administration"></a>Administração ao nível do inquilino

Para obter visibilidade em todos os gateways de dados no local de um inquilino AZure AD, os administradores globais nesse inquilino podem inscrever-se no [centro de administração da Power Platform](https://powerplatform.microsoft.com) como administrador de inquilinos e selecionar a opção Data **Gateways.** Para obter mais informações, consulte [a administração ao nível do inquilino para a porta de dados no local.](/data-integration/gateway/service-gateway-tenant-level-admin)

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Reiniciar o gateway

Por predefinição, a instalação gateway no seu computador local funciona como uma conta de serviço do Windows chamada "Serviço de gateway de dados no local". No entanto, a instalação gateway utiliza o `NT SERVICE\PBIEgwService` nome para as suas credenciais de conta "Log On As" e tem permissões de "Iniciar sessão como serviço".

> [!NOTE]
> A sua conta de serviço Windows difere da conta utilizada para ligar a fontes de dados no local e da conta Azure que utiliza quando iniciar seduca nos serviços na nuvem.

Como qualquer outro serviço Windows, pode iniciar e parar o gateway de várias maneiras. Para obter mais informações, consulte [Reiniciar uma porta de dados no local.](/data-integration/gateway/service-gateway-restart)

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Como funciona o gateway

Os utilizadores da sua organização podem aceder aos dados no local para os quais já tenham acesso autorizado. No entanto, antes de estes utilizadores poderem ligar-se à sua fonte de dados no local, é necessário instalar e configurar um portal de dados no local. Normalmente, um administrador é a pessoa que instala e instala um portal. Estas ações podem requerer permissões de Administrador do Servidor ou conhecimentos especiais sobre os seus servidores no local.

O portal ajuda a facilitar uma comunicação mais rápida e segura nos bastidores. Esta comunicação flui entre um utilizador na nuvem, o serviço de nuvem gateway e a sua fonte de dados no local. O serviço de nuvem gateway encripta e armazena as suas credenciais de origem de dados e detalhes do gateway. O serviço também encaminha consultas e os seus resultados entre o utilizador, o gateway e a sua fonte de dados no local.

O gateway funciona com firewalls e utiliza apenas ligações de saída. Todo o tráfego tem origem como tráfego de saída seguro do agente de gateway. O gateway envia os dados de fontes no local em canais encriptados através de [mensagens de autocarro do serviço Azure.](../service-bus-messaging/service-bus-messaging-overview.md) Este autocarro de serviço cria um canal entre o gateway e o serviço de chamadas, mas não armazena quaisquer dados. Todos os dados que viajam através do portal estão encriptados.

![Arquitetura para porta de dados no local](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> Dependendo do serviço de nuvem, poderá ser necessário configurar uma fonte de dados para o gateway.

Estes passos descrevem o que acontece quando interage com um elemento ligado a uma fonte de dados no local:

1. O serviço na nuvem cria uma consulta, juntamente com as credenciais encriptadas para a fonte de dados. Em seguida, o serviço envia a consulta e as credenciais para a fila do gateway para processamento.

1. O serviço de nuvem gateway analisa a consulta e empurra o pedido para a Azure Service Bus Messaging.

1. A Azure Service Bus Messaging envia os pedidos pendentes para o portal.

1. O gateway obtém a consulta, desencripta as credenciais e estabelece ligação a uma ou mais origens de dados com essas credenciais.

1. O portal envia a consulta para a fonte de dados para correr.

1. Os resultados são enviados da fonte de dados de volta para o gateway, e depois para o serviço de nuvem gateway. O serviço de nuvem gateway utiliza então os resultados.

### <a name="authentication-to-on-premises-data-sources"></a>Autenticação em origens de dados no local

Uma credencial armazenada é usada para ligar do gateway a fontes de dados no local. Independentemente do utilizador, o gateway utiliza a credencial armazenada para se ligar. Pode haver exceções de autenticação para serviços específicos, tais como DirectQuery e LiveConnect para serviços de análise em Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Os serviços na nuvem da Microsoft utilizam [o Azure AD](../active-directory/fundamentals/active-directory-whatis.md) para autenticar os utilizadores. Um inquilino da AD Azure contém nomes de utilizador e grupos de segurança. Normalmente, o endereço de e-mail que utiliza para iniciar sôm é o mesmo que o Nome Principal do Utilizador (UPN) para a sua conta.

### <a name="what-is-my-upn"></a>Qual é a minha UPN?

Se não és um administrador de domínio, podes não conhecer a TUA UPN. Para encontrar a UPN para a sua conta, gere o `whoami /upn` comando a partir do seu posto de trabalho. Embora o resultado pareça um endereço de e-mail, o resultado é a UPN para a sua conta de domínio local.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Sincronizar uma conta do Active Directory no local com o Azure AD

A UPN para as suas contas de Ative Directy e Azure AD devem ser as mesmas. Por isso, certifique-se de que cada conta ative directy no local corresponde à sua conta Azure AD. Os serviços na nuvem só sabem sobre contas dentro do Azure AD. Então, não precisa adicionar uma conta ao seu Diretório Ativo no local. Se a conta não existir no Azure AD, não pode usar essa conta.

Aqui estão as formas de combinar as suas contas de Ative Directory no local com a Azure AD.

* Adicione as contas manualmente ao Azure AD.

  Crie uma conta no portal Azure ou no centro de administração microsoft 365. Certifique-se de que o nome da conta corresponde à UPN para a conta ative do Diretório no local.

* Sincronizar as contas locais para o seu inquilino Azure AD utilizando a ferramenta Azure Ative Directory Connect.

  A ferramenta Azure AD Connect fornece opções para sincronização de diretórios e configuração de autenticação. Estas opções incluem sincronização de haxixe de palavra-passe, autenticação de passagem e federação. Se você não é um administrador inquilino ou um administrador de domínio local, contacte o seu administrador de TI para configurar o Azure AD Connect. O Azure AD Connect garante que o seu Azure AD UPN corresponde ao seu Diretório Ativo local UPN. Esta correspondência ajuda se estiver a utilizar ligações ao vivo dos Serviços de Análise com capacidades de Power BI ou de um único sign-on (SSO).

  > [!NOTE]
  > Sincronizar contas com a ferramenta Azure AD Connect cria novas contas no seu inquilino AZure AD.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>FAQ e resolução de problemas

* [FAQ do gateway de dados no local](/data-integration/gateway/service-gateway-onprem-faq)
* [Resolução de problemas do gateway de dados no local](/data-integration/gateway/service-gateway-tshoot)
* [Monitorizar e otimizar o desempenho de gateways](/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Passos seguintes

* [Conecte-se aos dados no local a partir de aplicações lógicas](../logic-apps/logic-apps-gateway-connection.md)
* [Funcionalidades de integração empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Conectores do Azure Logic Apps](../connectors/apis-list.md)
