---
title: Integrar o RDG com a extensão NPS do Azure MFA-Azure Active Directory
description: Integrar a sua infraestrutura de Gateway de ambiente de trabalho remoto com a MFA do Azure com a extensão de servidor de políticas de rede para o Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ec402cf2c741d88d230e5734485bf9eb0dd1b03
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381818"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrar a sua infraestrutura de Gateway de ambiente de trabalho remoto usando a extensão de servidor de políticas de rede (NPS) e o Azure AD

Este artigo fornece detalhes para integrar a sua infraestrutura de Gateway de ambiente de trabalho remoto com o Azure multi-factor Authentication (MFA) com a extensão de servidor de políticas de rede (NPS) para o Microsoft Azure.

A extensão do servidor de políticas de rede (NPS) para o Azure permite que os clientes protejam a autenticação de cliente do serviço RADIUS (RADIUS) usando a [MFA (autenticação multifator](multi-factor-authentication.md)baseada em nuvem) do Azure. Esta solução fornece verificação de dois passos para adicionar uma segunda camada de segurança para inícios de sessão de utilizador e transações.

Este artigo fornece instruções passo a passo para integrar a infraestrutura NPS com o MFA do Azure utilizando a extensão NPS para Azure. Isto permite que a verificação segura para os utilizadores que tentarem iniciar sessão para um Gateway de ambiente de trabalho remoto.

> [!NOTE]
> Este artigo não deve ser usado com implantações de servidor MFA e deve ser usado somente com implantações do Azure MFA (baseadas em nuvem).

A política de rede e serviços de acesso (NPS) dá às organizações a capacidade de fazer o seguinte:

* Defina localizações centrais para o gerenciamento e controle de pedidos de rede ao especificar quem pode se conectar, que alturas de ligações de dia são permitidos, a duração de ligações e o nível de segurança que os clientes devem utilizar para ligar e assim por diante. Em vez de especificar estas políticas em cada servidor VPN ou Gateway de ambiente de trabalho remoto (RD), estas políticas podem ser especificadas uma vez numa localização central. O protocolo RADIUS fornece a autenticação, autorização e contabilidade (AAA) centralizadas.
* Estabelecer e impor políticas de estado de funcionamento de cliente de proteção de acesso de rede (NAP) que determinam se os dispositivos recebem acesso restrito ou irrestrito aos recursos de rede.
* Fornecer um meio para impor a autenticação e autorização de acesso 802.1 pontos de acesso sem fio com capacidade de x e comutadores de Ethernet.

Normalmente, as organizações usam o NPS (RADIUS) para simplificar e centralizar o gerenciamento de políticas de VPN. No entanto, muitas organizações também utilizam o NPS para simplificar e centralizar o gerenciamento de diretivas de autorização de ligação de ambiente de trabalho de área de trabalho remota (CAPs de RD).

As organizações também podem integrar o NPS com a MFA do Azure para melhorar a segurança e fornecer um alto nível de conformidade. Isto ajuda a garantir que os usuários estabelecer a verificação de dois passos para iniciar sessão para o Gateway de ambiente de trabalho remoto. Para os utilizadores ter acesso, tem de fornecer sua combinação de nome de utilizador/palavra-passe, juntamente com informações que o utilizador tem no seu controle. Estas informações tem de ser fidedigno e não facilmente duplicadas, como um número de telefone celular, o número fixo, a aplicação num dispositivo móvel e assim por diante. Atualmente, o RDG dá suporte a chamadas telefônicas e notificações por push dos métodos de aplicativo Microsoft Authenticator para 2FA. Para obter mais informações sobre os métodos de autenticação com suporte, consulte a seção [determinar quais métodos de autenticação seus usuários podem usar](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Antes da disponibilidade da extensão do NPS para o Azure, os clientes que desejavam implementar a verificação em duas etapas para ambientes integrados do NPS e do Azure MFA tinham que configurar e manter um servidor MFA separado no ambiente local, conforme documentado em [área de trabalho remota gateway e no Azure servidor de autenticação multifator usando RADIUS](howto-mfaserver-nps-rdg.md).

A disponibilidade da extensão NPS para o Azure proporciona às organizações a opção de implementar uma solução MFA no local com base ou uma solução MFA baseada na nuvem para autenticação de cliente RADIUS segura.

## <a name="authentication-flow"></a>Fluxo de autenticação

Para os utilizadores ter acesso a recursos de rede através de um Gateway de ambiente de trabalho remoto, tem de cumprir as condições especificadas na política de autorização uma conexão de área de trabalho remota (CAP de RD) e a política de autorização um recursos de área de trabalho remota (RAP de RD). CAPs de RD especificar quem está autorizado para se ligar aos Gateways de área de trabalho remota. RD RAPs especificar os recursos de rede, como áreas de trabalho remotas ou remoto, o que o utilizador tem permissão para ligar a através do Gateway de RD.

Um Gateway de RD pode ser configurado para utilizar um arquivo de política central de CAPs de RD. RD RAPs não é possível utilizar uma política central, como são processados no Gateway de RD. Um exemplo de um Gateway de RD configurados para utilizar um arquivo de política central de CAPs de RD é um cliente RADIUS para outro servidor NPS que funciona como o arquivo de política central.

Quando a extensão NPS para Azure está integrada com o NPS e o Gateway de ambiente de trabalho remoto, o fluxo de autenticação com êxito é o seguinte:

1. O servidor de Gateway de ambiente de trabalho remoto recebe um pedido de autenticação de um usuário de área de trabalho remoto para ligar a um recurso, como uma sessão de ambiente de trabalho remoto. Atuando como um cliente RADIUS, o servidor de Gateway de ambiente de trabalho remoto converte o pedido para uma mensagem de pedido de acesso RADIUS e envia a mensagem para o servidor RADIUS (NPS), onde está instalada a extensão NPS.
1. A combinação de nome de utilizador e palavra-passe é verificar no Active Directory e o utilizador é autenticado.
1. Se forem cumpridos todas as condições conforme especificado no pedido de ligação de NPS e as políticas de rede (por exemplo, a hora do dia ou grupo de restrições de associação), a extensão NPS aciona um pedido de autenticação secundária com o MFA do Azure.
1. MFA do Azure comunica com o Azure AD, obtém os detalhes do utilizador e efetua a autenticação secundária através de métodos suportados.
1. Após a conclusão bem-sucedida do desafio MFA, MFA do Azure comunica o resultado para a extensão NPS.
1. O servidor NPS, onde está instalada a extensão, envia uma mensagem de aceitar de acesso RADIUS de mensagens em fila para a política de CAP de RD para o servidor de Gateway de ambiente de trabalho remoto.
1. O utilizador é concedido acesso ao recurso pedido de rede através do Gateway de RD.

## <a name="prerequisites"></a>Pré-requisitos

Esta secção fornece detalhes sobre os pré-requisitos necessários antes de integrar o MFA do Azure com o Gateway de ambiente de trabalho remoto. Antes de começar, tem de ter os seguintes pré-requisitos no local.  

* Infraestrutura remota de serviços de ambiente de trabalho (RDS)
* Licença do MFA do Azure
* Software Windows Server
* Função de política de rede e serviços de acesso (NPS)
* O Azure Active Directory sincronizado com o Active Directory no local
* ID de GUID do Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Infraestrutura remota de serviços de ambiente de trabalho (RDS)

Tem de ter uma infraestrutura de serviços de ambiente de trabalho remoto (RDS) funcional no local. Se você não fizer isso, poderá criar rapidamente essa infraestrutura no Azure usando o seguinte modelo de início rápido: [criar área de trabalho remota implantação de coleção de sessão](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Se quiser criar manualmente uma infraestrutura RDS no local rapidamente para fins de teste, siga os passos para implementar uma.
**Saiba mais**: [implante o RDS com o início rápido do Azure e a](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) [implantação da infraestrutura básica do RDS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-mfa-license"></a>Licença do MFA do Azure

Necessário é uma licença do MFA do Azure, que está disponível através do Azure AD Premium ou outros pacotes que incluem-lo. Baseado no consumo licenças do MFA do Azure, de acordo com o utilizador ou por licenças de autenticação, não são compatíveis com a extensão NPS. Para obter mais informações, consulte [como obter a autenticação multifator do Azure](concept-mfa-licensing.md). Para fins de teste, pode utilizar uma subscrição de avaliação.

### <a name="windows-server-software"></a>Software Windows Server

A extensão NPS requer o Windows Server 2008 R2 SP1 ou superior com o serviço de função NPS instalado. Todos os passos nesta secção foram efetuados com o Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Função de política de rede e serviços de acesso (NPS)

O serviço de função NPS fornece o servidor RADIUS e o cliente, funcionalidades, bem como o serviço de estado de funcionamento de política de acesso de rede. Esta função tem de ser instalada em, pelo menos, dois computadores na sua infraestrutura: O Gateway de ambiente de trabalho remoto e outro servidor membro ou controlador de domínio. Por predefinição, a função já está presente no computador configurado como o Gateway de ambiente de trabalho remoto.  Tem também de instalar a função NPS em, pelo menos, noutro computador, tal como um controlador de domínio ou servidor membro.

Para obter informações sobre como instalar o serviço de função NPS do Windows Server 2012 ou mais antigo, consulte [instalar um servidor de política de integridade de NAP](https://technet.microsoft.com/library/dd296890.aspx). Para obter uma descrição das práticas recomendadas para o NPS, incluindo a recomendação para instalar o NPS em um controlador de domínio, consulte [práticas recomendadas para o NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>O Azure Active Directory sincronizado com o Active Directory no local

Para utilizar a extensão NPS, os utilizadores no local tem de ser sincronizados com o Azure AD e ativados para a MFA. Esta secção assume que os utilizadores no local estão sincronizados com o Azure AD com o AD Connect. Para obter informações sobre o Azure AD Connect, consulte [integrar seus diretórios locais com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>ID de GUID do Azure Active Directory

Para instalar a extensão NPS, precisa saber o GUID do Azure AD. Abaixo, são fornecidas instruções para localizar o GUID do Azure AD.

## <a name="configure-multi-factor-authentication"></a>Configurar o multi-factor Authentication

Esta secção fornece instruções para integrar o MFA do Azure com o Gateway de ambiente de trabalho remoto. Como administrador, tem de configurar o serviço de MFA do Azure antes dos utilizadores Self-podem registar os dispositivos de multi-factor ou aplicações.

Siga as etapas em [introdução à autenticação multifator do Azure na nuvem](howto-mfa-getstarted.md) para habilitar a MFA para seus usuários do Azure AD.

### <a name="configure-accounts-for-two-step-verification"></a>Configurar contas para verificação de dois passos

Depois de uma conta tiver sido ativada para a MFA, não é possível iniciar sessão nos recursos regidos pela política de MFA até que configurou com êxito um dispositivo fidedigno para utilizar para o segundo fator de autenticação e autenticado usando a verificação de dois passos.

Siga as etapas em [o que a autenticação multifator do Azure significa para mim?](../user-help/multi-factor-authentication-end-user.md) para entender e configurar corretamente seus dispositivos para MFA com sua conta de usuário.

## <a name="install-and-configure-nps-extension"></a>Instalar e configurar a extensão NPS

Esta secção fornece instruções para configurar a infraestrutura RDS para utilizar o MFA do Azure para a autenticação de cliente com o Gateway de ambiente de trabalho remoto.

### <a name="acquire-azure-active-directory-guid-id"></a>Adquirir ID de GUID do Azure Active Directory

Como parte da configuração da extensão NPS, terá de fornecer credenciais de administrador e o ID do Azure AD para o seu inquilino do Azure AD. Os passos seguintes mostram como obter o ID do inquilino.

1. Entre no [portal do Azure](https://portal.azure.com) como o administrador global do locatário do Azure.
1. No painel de navegação esquerdo, selecione o ícone de **Azure Active Directory** .
1. Selecione **Propriedades**.
1. Na folha Propriedades, ao lado da ID do diretório, clique no ícone de **cópia** , conforme mostrado abaixo, para copiar a ID para a área de transferência.

   ![Obtendo a ID do diretório do portal do Azure](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>Instalar a extensão NPS

Instale a extensão NPS num servidor que tenha a função de política de rede e serviços de acesso (NPS) instalada. Isso funciona como servidor RADIUS para o design.

> [!Important]
> Certifique-se de que não instalar a extensão NPS no servidor de Gateway de ambiente de trabalho remoto.
>

1. Baixe a [extensão NPS](https://aka.ms/npsmfa).
1. Copie o ficheiro executável do programa de configuração (NpsExtnForAzureMfaInstaller.exe) para o servidor NPS.
1. No servidor NPS, clique duas vezes em **NpsExtnForAzureMfaInstaller. exe**. Se solicitado, clique em **executar**.
1. Na caixa de diálogo extensão de NPS para a instalação do Azure MFA, examine os termos de licença de software, marque **eu concordo com os termos e condições de licença**e clique em **instalar**.
1. Na caixa de diálogo extensão do NPS para a instalação do Azure MFA, clique em **fechar**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configurar certificados para utilização com a extensão NPS utilizando um script do PowerShell

Em seguida, terá de configurar certificados para utilização pela extensão de NPS para garantir a proteger as comunicações e garantia. Os componentes NPS incluem um script do Windows PowerShell que configura um certificado autoassinado para utilização com NPS.

O script realiza as seguintes ações:

* Cria um certificado autoassinado
* Associa a chave pública do certificado para o serviço principal no Azure AD
* Armazena o certificado no arquivo do computador local
* Concede acesso a chave privada do certificado para o utilizador de rede
* Reinicia o serviço de servidor de políticas de rede

Se desejar usar seus próprios certificados, terá de associar a chave pública do certificado para o principal de serviço no Azure AD e assim por diante.

Para utilizar o script, forneça a extensão com as credenciais de administrador do Azure AD e o ID de inquilino do Azure AD que copiou anteriormente. Execute o script em cada servidor NPS onde instalou a extensão NPS. Em seguida, faça o seguinte:

1. Abra uma linha administrativa do Windows PowerShell.
1. No prompt do PowerShell, digite `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`e pressione **Enter**.
1. Digite `.\AzureMfaNpsExtnConfigSetup.ps1`e pressione **Enter**. O script verifica se o módulo Azure Active Directory PowerShell está instalado. Se não instalado, o script instala o módulo para.

   ![Executando AzureMfaNpsExtnConfigSetup. ps1 no PowerShell do Azure AD](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Depois do script verifica a instalação do módulo do PowerShell, ele exibe a caixa de diálogo de módulo do Azure Active Directory PowerShell. Na caixa de diálogo, insira suas credenciais e senha de administrador do Azure AD e clique em **entrar**.

   ![Autenticando no Azure AD no PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Quando solicitado, Cole a ID do diretório que você copiou para a área de transferência anteriormente e pressione **Enter**.

   ![Inserindo a ID de diretório no PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. O script cria um certificado autoassinado e realiza outras alterações de configuração. O resultado deve ser semelhante à imagem abaixo.

   ![Saída do PowerShell mostrando o certificado autoassinado](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Configurar componentes do NPS no Gateway de ambiente de trabalho remoto

Nesta secção, irá configurar as políticas de autorização de ligação de Gateway de ambiente de trabalho remoto e outras definições de RADIUS.

O fluxo de autenticação requer que as mensagens RADIUS sejam trocadas entre o gateway de Área de Trabalho Remota e o servidor NPS onde a extensão NPS está instalada. Isso significa que tem de configurar as definições de cliente RADIUS no Gateway de ambiente de trabalho remoto e o servidor NPS de onde está instalada a extensão NPS.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Configurar políticas de autorização de ligação de Gateway de ambiente de trabalho remoto para utilizar o armazenamento central

Políticas de autorização de conexão de área de trabalho remoto (CAPs de RD) especificam os requisitos para ligar a um servidor de Gateway de ambiente de trabalho remoto. CAPs de RD podem ser armazenados localmente (predefinição) ou podem ser armazenados num armazenamento central de CAP de RD que executa o NPS. Para configurar a integração do MFA do Azure com o RDS, terá de especificar a utilização de um armazenamento central.

1. No servidor de gateway de área de trabalho remota, abra **Gerenciador do servidor**.
1. No menu, clique em **ferramentas**, aponte para **serviços de área de trabalho remota**e clique em **área de trabalho remota Gerenciador de gateway**.
1. No Gerenciador de gateway de área de trabalho remota, clique com o botão direito do mouse em **\[nome do servidor\] (local)** e clique em **Propriedades**.
1. Na caixa de diálogo Propriedades, selecione a guia **armazenamento de RD CAP** .
1. Na guia armazenamento de RD CAP, selecione **servidor central executando o NPS**. 
1. No campo **Inserir um nome ou endereço IP para o servidor que está executando o NPS** , digite o endereço IP ou o nome do servidor do servidor onde você instalou a extensão NPS.

   ![Insira o nome ou endereço IP do servidor NPS](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Clique em **Adicionar**.
1. Na caixa de diálogo **segredo compartilhado** , insira um segredo compartilhado e clique em **OK**. Certifique-se de que registe este segredo partilhado e guarde o registo de forma segura.

   >[!NOTE]
   >Segredo partilhado é utilizado para estabelecer confiança entre os clientes e servidores RADIUS. Crie um segredo longo e complexo.
   >

   ![Criando um segredo compartilhado para estabelecer confiança](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Clique em **OK** para fechar a caixa de diálogo.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Configurar o valor de tempo limite RADIUS no NPS de Gateway de ambiente de trabalho remoto

Para garantir que há tempo para validar as credenciais dos utilizadores, efetuar a verificação de dois passos, receber respostas e responder a mensagens RADIUS, é necessário ajustar o valor de tempo limite RADIUS.

1. No servidor de Gateway de RD, abra o Gestor de servidor. No menu, clique em **ferramentas**e, em seguida, clique em **servidor de políticas de rede**.
1. No console do **NPS (local)** , expanda **clientes e servidores RADIUS**e selecione **servidor RADIUS remoto**.

   ![Console de gerenciamento do servidor de políticas de rede mostrando servidor RADIUS remoto](./media/howto-mfa-nps-extension-rdg/image12.png)

1. No painel de detalhes, clique duas vezes em **grupo de servidores Gateway TS**.

   >[!NOTE]
   >Este grupo de servidor RADIUS foi criado quando configurou o servidor central para políticas NPS. O Gateway de RD encaminha mensagens RADIUS para este servidor ou o grupo de servidores, se mais de um grupo.
   >

1. Na caixa de diálogo **Propriedades do grupo de servidores Gateway TS** , selecione o endereço IP ou o nome do servidor NPS que você configurou para armazenar RD CAPs e clique em **Editar**.

   ![Selecione o IP ou o nome do servidor NPS configurado anteriormente](./media/howto-mfa-nps-extension-rdg/image13.png)

1. Na caixa de diálogo **editar servidor RADIUS** , selecione a guia **balanceamento de carga** .
1. Na guia **balanceamento de carga** , no campo **número de segundos sem resposta antes que a solicitação seja considerada descartada** , altere o valor padrão de 3 para um valor entre 30 e 60 segundos.
1. No campo **número de segundos entre as solicitações quando o servidor é identificado como indisponível** , altere o valor padrão de 30 segundos para um valor que seja igual ou maior que o valor especificado na etapa anterior.

   ![Editar configurações de tempo limite do servidor RADIUS na guia balanceamento de carga](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Clique em **OK** duas vezes para fechar as caixas de diálogo.

### <a name="verify-connection-request-policies"></a>Verifique se as diretivas de solicitação de conexão

Por predefinição, quando configurar o Gateway de RD para utilizar um arquivo de política central de diretivas de autorização de conexão, o Gateway de RD está configurado para reencaminhar pedidos de limite para o servidor NPS. O servidor NPS com a extensão de MFA do Azure instalada, processa o pedido de acesso RADIUS. Os passos seguintes mostram-lhe como verificar a política de pedido de ligação predefinida.

1. No gateway de área de trabalho remota, no console do NPS (local), expanda **políticas**e selecione **políticas de solicitação de conexão**.
1. Clique duas vezes em **política de autorização de Gateway TS**.
1. Na caixa de diálogo **Propriedades da política de autorização de Gateway TS** , clique na guia **configurações** .
1. Na guia **configurações** , em encaminhando a solicitação de conexão, clique em **autenticação**. Cliente RADIUS está configurado para reencaminhar pedidos de autenticação.

   ![Definir configurações de autenticação especificando o grupo de servidores](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Clique em **Cancelar**.

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Configurar o NPS no servidor onde está instalada a extensão NPS

O servidor NPS de onde está instalada a extensão NPS tem de ser capaz de trocar mensagens RADIUS com o servidor NPS no Gateway de ambiente de trabalho remoto. Para ativar essa troca de mensagens, terá de configurar os componentes NPS no servidor onde está instalado o serviço de extensão NPS.

### <a name="register-server-in-active-directory"></a>Registar o servidor no Active Directory

Para funcionar corretamente neste cenário, o servidor NPS tem de ser registado no Active Directory.

1. No servidor NPS, abra **Gerenciador do servidor**.
1. Em Gerenciador do Servidor, clique em **ferramentas**e, em seguida, clique em **servidor de políticas de rede**.
1. No console do servidor de diretivas de rede, clique com o botão direito do mouse em **NPS (local)** e, em seguida, clique em **registrar servidor em Active Directory**.
1. Clique em **OK** duas vezes.

   ![Registrar o servidor NPS no Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Deixe a consola aberta para o próximo procedimento.

### <a name="create-and-configure-radius-client"></a>Criar e configurar o cliente RADIUS

O Gateway de ambiente de trabalho remoto tem de ser configurado como um cliente RADIUS para o servidor NPS.

1. No servidor NPS em que a extensão NPS está instalada, no console **NPS (local)** , clique com o botão direito do mouse em **clientes RADIUS** e clique em **novo**.

   ![Criar um novo cliente RADIUS no console do NPS](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Na caixa de diálogo **novo cliente RADIUS** , forneça um nome amigável, como _Gateway_, e o endereço IP ou o nome DNS do servidor de gateway de área de trabalho remota.
1. Nos campos **segredo compartilhado** e **confirmar segredo compartilhado** , insira o mesmo segredo que você usou antes.

   ![Configurar um nome amigável e o endereço IP ou DNS](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Clique em **OK** para fechar a caixa de diálogo novo cliente RADIUS.

### <a name="configure-network-policy"></a>Configurar a política de rede

Lembre-se de que o servidor NPS com a extensão de MFA do Azure é o arquivo de política central designado para a política de autorização de ligação (CAP). Portanto, precisa implementar um limite no servidor NPS para autorizar solicitações de conexão válida.  

1. No servidor NPS, abra o console do NPS (local), expanda **políticas**e clique em **políticas de rede**.
1. Clique com o botão direito do mouse em **conexões com outros servidores de acesso**e clique em **política duplicada**.

   ![Duplicar a conexão com outra política de servidores de acesso](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Clique com o botão direito do mouse em **cópia de conexões para outros servidores de acesso**e clique em **Propriedades**.
1. Na caixa **de diálogo copiar conexões para outros servidores de acesso** , em **nome da política**, insira um nome adequado, como _RDG_CAP_. Verifique a **política habilitada**e selecione **conceder acesso**. Opcionalmente, em **tipo de servidor de acesso à rede**, selecione **área de trabalho remota gateway**ou você pode deixá-lo como **não especificado**.

   ![Nomear a política, habilitar e conceder acesso](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Clique na guia **restrições** e marque **permitir que os clientes se conectem sem negociar um método de autenticação**.

   ![Modificar métodos de autenticação para permitir que os clientes se conectem](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Opcionalmente, clique na guia **condições** e adicione condições que devem ser atendidas para que a conexão seja autorizada, por exemplo, associação em um grupo específico do Windows.

   ![Opcionalmente, especifique as condições de conexão](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Clique em **OK**. Quando for solicitado a exibir o tópico da ajuda correspondente, clique em **não**.
1. Certifique-se de que a nova política é, na parte superior da lista, que a política está ativada, e que ele concede acesso.

   ![Mover sua política para o topo da lista](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Verificar configuração

Para verificar a configuração, terá de iniciar sessão para o Gateway de ambiente de trabalho remoto com um cliente RDP adequado. Certifique-se de que utilize uma conta que é permitida pelas suas diretivas de autorização de conexão e ativada para a MFA do Azure.

Conforme mostrado na imagem abaixo, você pode usar a página **acesso via Web à área de trabalho remota** .

![Testando no Acesso via Web à Área de Trabalho Remota](./media/howto-mfa-nps-extension-rdg/image25.png)

Ao inserir com êxito as suas credenciais para autenticação primária, a caixa de diálogo ligação de ambiente de trabalho remoto apresentem o estado a iniciar a ligação remota, conforme mostrado abaixo. 

Se autenticar com êxito com o método de autenticação secundária que configurou anteriormente na MFA do Azure, está ligado ao recurso. No entanto, se a autenticação secundária não for bem-sucedida, for negado o acesso ao recurso. 

![Conexão de Área de Trabalho Remota iniciar uma conexão remota](./media/howto-mfa-nps-extension-rdg/image26.png)

No exemplo abaixo, a aplicação de autenticador num telemóvel Windows é utilizada para fornecer a autenticação secundária.

![Exemplo de aplicativo autenticador Windows Phone mostrando a verificação](./media/howto-mfa-nps-extension-rdg/image27.png)

Depois de ter autenticado com êxito usando o método de autenticação secundária, esteja conectado ao Gateway de ambiente de trabalho remoto como habitualmente. No entanto, como você precisa usar um método de autenticação secundário usando um aplicativo móvel em um dispositivo confiável, o processo de entrada é mais seguro do que seria.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Ver registos do Visualizador de eventos para eventos de início de sessão bem-sucedidos

Para ver os eventos de início de sessão com êxito nos registos do Visualizador de eventos do Windows, pode emitir o seguinte comando do Windows PowerShell para consultar os registos de serviços de Terminal do Windows e de segurança do Windows.

Para consultar eventos de entrada bem-sucedidos nos logs operacionais do gateway _(Viewer\Applications de eventos e serviços Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_ , use os seguintes comandos do PowerShell:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Este comando apresenta os eventos do Windows que mostram o usuário cumpridos os requisitos de política de autorização de recursos (RAP de RD) e foi concedido acesso.

![Exibindo eventos usando o PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Este comando apresenta os eventos que apresentam ao usuário cumpridos os requisitos de política de autorização de conexão.

![exibindo a política de autorização de conexão usando o PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Também pode ver este registo e o filtro no evento IDs, 300 e 200. Para consultar os eventos de início de sessão bem-sucedidos nos logs de Visualizador de eventos de segurança, utilize o seguinte comando:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Este comando pode ser executado no central NPS ou o servidor de Gateway de RD.

![Eventos de logon bem-sucedidos de exemplo](./media/howto-mfa-nps-extension-rdg/image30.png)

Também pode ver o registo de segurança ou a vista personalizada serviços de acesso e política de rede, conforme mostrado abaixo:

![Serviços de acesso e política de rede Visualizador de Eventos](./media/howto-mfa-nps-extension-rdg/image31.png)

No servidor em que você instalou a extensão NPS para o Azure MFA, você pode encontrar Visualizador de Eventos logs de aplicativo específicos para a extensão em _Application and Services Logs\Microsoft\AzureMfa_.

![Logs de aplicativo Visualizador de Eventos AuthZ](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Guia de resolução de problemas

Se a configuração não está a funcionar conforme esperado, o primeiro lugar para começar a resolução de problemas é verificar se o utilizador está configurado para utilizar o MFA do Azure. Peça ao usuário para se conectar ao [portal do Azure](https://portal.azure.com). Se os utilizadores recebem um pedido de verificação secundária e com êxito podem autenticar, pode eliminar uma configuração incorreta do MFA do Azure.

Se a MFA do Azure está a funcionar para o utilizador (es), deve rever os registos de eventos relevantes. Estes incluem o evento de segurança, Gateway operacional e registos de MFA do Azure que são abordados na secção anterior.

Segue-se uma saída de exemplo do registo de segurança que mostra um evento de início de sessão (6273 de ID de evento).

![Exemplo de um evento de logon com falha](./media/howto-mfa-nps-extension-rdg/image33.png)

Segue-se um evento relacionado dos AzureMFA logs:

![Exemplo de log do Azure MFA no Visualizador de Eventos](./media/howto-mfa-nps-extension-rdg/image34.png)

Para efetuar advanced opções de resolução de problemas, consulte os ficheiros de registo do formato do NPS da base de dados onde o serviço NPS está instalado. Esses arquivos de log são criados na pasta _%systemroot%\System32\Logs_ como arquivos de texto delimitados por vírgula.

Para obter uma descrição desses arquivos de log, consulte [interpretar arquivos de log de formato de banco de dados NPS](https://technet.microsoft.com/library/cc771748.aspx). As entradas nesses arquivos de log podem ser difíceis de interpretar sem importá-los numa planilha ou um banco de dados. Pode encontrar vários analisadores de IAS online para ajudá-lo a interpretar os ficheiros de registo.

A imagem abaixo mostra a saída de um desses [aplicativos Shareware](https://www.deepsoftware.com/iasviewer)que pode ser baixado.

![Analisador de IAS do aplicativo shareware de exemplo](./media/howto-mfa-nps-extension-rdg/image35.png)

Por fim, para obter mais opções de solução de problemas, você pode usar um analisador de protocolo, como [o Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

A imagem abaixo do Microsoft Message Analyzer mostra o tráfego de rede filtrado no protocolo RADIUS que contém o nome de usuário **CONTOSO\AliceC**.

![Analisador de mensagem da Microsoft mostrando tráfego filtrado](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Passos seguintes

[How to get Azure Multi-Factor Authentication](concept-mfa-licensing.md) (Como obter a Multi-Factor Authentication do Azure)

[Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS](howto-mfaserver-nps-rdg.md)

[Integrar os diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
