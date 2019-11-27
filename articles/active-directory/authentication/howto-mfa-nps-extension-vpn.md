---
title: VPN com o Azure MFA usando a extensão do NPS-Azure Active Directory
description: Integre sua infraestrutura de VPN com o Azure MFA usando a extensão de servidor de políticas de rede para Microsoft Azure.
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
ms.openlocfilehash: fa0158b99d10b426efb02ca31cef2bc0053a976f
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404680"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrar sua infraestrutura de VPN com o Azure MFA usando a extensão do servidor de políticas de rede para o Azure

A extensão do servidor de políticas de rede (NPS) para o Azure permite que as organizações protejam a autenticação de cliente do serviço RADIUS (RADIUS) usando a [MFA (autenticação multifator) do Azure](howto-mfaserver-nps-rdg.md)baseada em nuvem, que fornece verificação em duas etapas.

Este artigo fornece instruções para integrar a infraestrutura do NPS com o MFA usando a extensão do NPS para o Azure. Esse processo permite a verificação em duas etapas segura para os usuários que tentam se conectar à sua rede usando uma VPN.

Os serviços de acesso e política de rede oferecem às organizações a capacidade de:

* Atribua um local central para o gerenciamento e o controle de solicitações de rede para especificar:

  * Quem pode se conectar

  * Quais horas do dia as conexões são permitidas

  * A duração das conexões

  * O nível de segurança que os clientes devem usar para se conectar

    Em vez de especificar políticas em cada VPN ou servidor gateway Área de Trabalho Remota, faça isso depois que estiverem em um local central. O protocolo RADIUS é usado para fornecer autenticação, autorização e contabilização centralizada (AAA).

* Estabelecer e impor políticas de estado de funcionamento de cliente de proteção de acesso de rede (NAP) que determinam se os dispositivos recebem acesso restrito ou irrestrito aos recursos de rede.

* Forneça uma maneira de impor a autenticação e a autorização para acessar pontos de acesso sem fio compatíveis com 802.1 x e comutadores Ethernet.
  Para obter mais informações, consulte [servidor de políticas de rede](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top).

Para aumentar a segurança e fornecer um alto nível de conformidade, as organizações podem integrar o NPS com a autenticação multifator do Azure para garantir que os usuários usem a verificação em duas etapas para se conectarem à porta virtual no servidor VPN. Para que os usuários recebam acesso, eles devem fornecer sua combinação de nome de usuário e senha e outras informações que eles controlam. Essas informações devem ser confiáveis e não facilmente duplicadas. Ele pode incluir um número de telefone celular, um número fixo ou um aplicativo em um dispositivo móvel.

Antes da disponibilidade da extensão NPS para o Azure, os clientes que desejavam implementar a verificação em duas etapas para ambientes integrados de NPS e MFA precisavam configurar e manter um servidor MFA separado em um ambiente local. Esse tipo de autenticação é oferecido pelo Área de Trabalho Remota gateway e pelo Azure Servidor de Autenticação Multifator usando o RADIUS.

Com a extensão do NPS para o Azure, as organizações podem proteger a autenticação de cliente RADIUS implantando uma solução MFA baseada no local ou uma solução MFA baseada em nuvem.

## <a name="authentication-flow"></a>Fluxo de autenticação

Quando os usuários se conectam a uma porta virtual em um servidor VPN, eles devem primeiro se autenticar usando uma variedade de protocolos. Os protocolos permitem o uso de uma combinação de nome de usuário e de senha e métodos de autenticação baseados em certificado.

Além de autenticar e verificar sua identidade, os usuários devem ter as permissões de discagem apropriadas. Em implementações simples, as permissões de discagem que permitem o acesso são definidas diretamente na Active Directory objetos de usuário.

![Guia de discagem em Propriedades do usuário do Active Directory usuários e computadores](./media/howto-mfa-nps-extension-vpn/image1.png)

Em implementações simples, cada servidor VPN concede ou nega o acesso com base nas políticas definidas em cada servidor VPN local.

Em implementações maiores e mais escalonáveis, as políticas que concedem ou negam acesso VPN são centralizadas em servidores RADIUS. Nesses casos, o servidor VPN atua como um servidor de acesso (cliente RADIUS) que encaminha solicitações de conexão e mensagens de conta para um servidor RADIUS. Para se conectar à porta virtual no servidor VPN, os usuários devem ser autenticados e atender às condições definidas centralmente nos servidores RADIUS.

Quando a extensão do NPS para o Azure é integrada ao NPS, um fluxo de autenticação é bem-sucedido, da seguinte maneira:

1. O servidor VPN recebe uma solicitação de autenticação de um usuário de VPN que inclui o nome de usuários e a senha para se conectar a um recurso, como uma sessão de Área de Trabalho Remota.
2. Agindo como um cliente RADIUS, o servidor VPN converte a solicitação em uma mensagem de *solicitação de acesso* RADIUS e a envia (com uma senha criptografada) para o servidor RADIUS onde a extensão NPS está instalada.
3. A combinação de nome de usuário e senha é verificada em Active Directory. Se o nome de usuário ou a senha estiverem incorretos, o servidor RADIUS enviará uma mensagem *de rejeição de acesso* .
4. Se todas as condições, conforme especificado nas diretivas de rede e solicitação de conexão NPS, forem atendidas (por exemplo, hora do dia ou restrições de associação de grupo), a extensão do NPS disparará uma solicitação de autenticação secundária com a autenticação multifator do Azure.
5. A autenticação multifator do Azure se comunica com o Azure Active Directory, recupera os detalhes do usuário e executa a autenticação secundária usando o método configurado pelo usuário (chamada telefônica celular, mensagem de texto ou aplicativo móvel).
6. Quando o desafio do MFA for bem-sucedido, a autenticação multifator do Azure comunicará o resultado à extensão do NPS.
7. Após a tentativa de conexão ser autenticada e autorizada, o NPS em que a extensão é instalada envia uma mensagem de *aceitação de acesso* RADIUS ao servidor VPN (cliente RADIUS).
8. O usuário recebe acesso à porta virtual no servidor VPN e estabelece um túnel VPN criptografado.

## <a name="prerequisites"></a>Pré-requisitos

Esta seção detalha os pré-requisitos que devem ser concluídos para que você possa integrar o MFA à VPN. Antes de começar, você deve ter os seguintes pré-requisitos em vigor:

* Infraestrutura de VPN
* Função de serviços de acesso e política de rede
* Licença da autenticação multifator do Azure
* Software Windows Server
* Bibliotecas
* Azure Active Directory (AD do Azure) sincronizado com o Active Directory local
* ID de GUID do Azure Active Directory

### <a name="vpn-infrastructure"></a>Infraestrutura de VPN

Este artigo pressupõe que você tenha uma infraestrutura de VPN funcional que usa o Microsoft Windows Server 2016 e que seu servidor VPN não está configurado no momento para encaminhar solicitações de conexão a um servidor RADIUS. No artigo, você configura a infraestrutura de VPN para usar um servidor RADIUS central.

Se você não tiver uma infraestrutura de VPN funcionando em vigor, poderá criar rapidamente uma seguindo as orientações em vários tutoriais de configuração de VPN que você pode encontrar nos sites da Microsoft e de terceiros.

### <a name="the-network-policy-and-access-services-role"></a>A função de serviços de acesso e política de rede

Os serviços de acesso e política de rede fornecem a funcionalidade de cliente e servidor RADIUS. Este artigo pressupõe que você instalou a função de serviços de acesso e política de rede em um servidor membro ou controlador de domínio em seu ambiente. Neste guia, você configura o RADIUS para uma configuração de VPN. Instale a função de serviços de acesso e política de rede em um servidor *que não seja* o servidor VPN.

Para obter informações sobre como instalar o serviço de função de serviços de acesso e política de rede Windows Server 2012 ou posterior, consulte [instalar um servidor de política de integridade de NAP](https://technet.microsoft.com/library/dd296890.aspx). A NAP foi preterida no Windows Server 2016. Para obter uma descrição das práticas recomendadas para o NPS, incluindo a recomendação para instalar o NPS em um controlador de domínio, consulte [práticas recomendadas para o NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Licença do MFA do Azure

Uma licença é necessária para a autenticação multifator do Azure e está disponível por meio de um Azure AD Premium, Enterprise Mobility + Security ou uma licença autônoma de autenticação multifator. As licenças baseadas em consumo para o Azure MFA, como licenças por usuário ou por autenticação, não são compatíveis com a extensão do NPS. Para obter mais informações, consulte [como obter a autenticação multifator do Azure](concept-mfa-licensing.md). Para fins de teste, pode utilizar uma subscrição de avaliação.

### <a name="windows-server-software"></a>Software Windows Server

A extensão do NPS requer o Windows Server 2008 R2 SP1 ou posterior, com a função de serviços de acesso e política de rede instalada. Todas as etapas neste guia foram executadas com o Windows Server 2016.

### <a name="libraries"></a>Bibliotecas

As bibliotecas a seguir são instaladas automaticamente com a extensão NPS:

-   [Pacotes C++ redistribuíveis do Visual para Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Módulo Microsoft Azure Active Directory para Windows PowerShell versão 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Se o módulo Microsoft Azure Active Directory PowerShell ainda não estiver presente, ele será instalado com um script de configuração que você executará como parte do processo de instalação. Não há necessidade de instalar o módulo antecipadamente se ele ainda não estiver instalado.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory sincronizadas com o Active Directory local

Para usar a extensão NPS, os usuários locais devem ser sincronizados com Azure Active Directory e habilitados para MFA. Este guia pressupõe que os usuários locais são sincronizados com o Azure Active Directory via Azure AD Connect. As instruções para habilitar usuários para MFA são fornecidas abaixo.

Para obter informações sobre Azure AD Connect, consulte [integrar seus diretórios locais com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>ID de GUID do Azure Active Directory

Para instalar a extensão NPS, você precisa saber o GUID do Azure Active Directory. As instruções para localizar o GUID do Azure Active Directory são fornecidas na próxima seção.

## <a name="configure-radius-for-vpn-connections"></a>Configurar o RADIUS para conexões VPN

Se você tiver instalado a função NPS em um servidor membro, precisará configurá-la para autenticar e autorizar o cliente VPN que solicita conexões VPN. 

Esta seção pressupõe que você instalou a função de serviços de acesso e política de rede, mas não a configurou para uso em sua infraestrutura.

> [!NOTE]
> Se você já tiver um servidor VPN em funcionamento que usa um servidor RADIUS centralizado para autenticação, poderá ignorar esta seção.
>

### <a name="register-server-in-active-directory"></a>Registar o servidor no Active Directory

Para funcionar corretamente nesse cenário, o servidor NPS deve ser registrado em Active Directory.

1. Abra Gerenciador do Servidor.

2. Em Gerenciador do Servidor, selecione **ferramentas**e, em seguida, selecione **servidor de políticas de rede**.

3. No console do servidor de diretivas de rede, clique com o botão direito do mouse em **NPS (local)** e selecione **registrar servidor em Active Directory**. Selecione **OK** duas vezes.

    ![Registrar servidor na opção de menu Active Directory](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Deixe a consola aberta para o próximo procedimento.

### <a name="use-wizard-to-configure-the-radius-server"></a>Usar o assistente para configurar o servidor RADIUS

Você pode usar uma opção padrão (baseada em assistente) ou configuração avançada para configurar o servidor RADIUS. Esta seção pressupõe que você esteja usando a opção de configuração padrão baseada em assistente.

1. No console do servidor de diretivas de rede, selecione **NPS (local)** .

2. Em **configuração padrão**, selecione **servidor RADIUS para conexões dial-up ou VPN**e, em seguida, selecione **Configurar VPN ou dial-up**.

    ![Configurar o servidor RADIUS para conexões dial-up ou VPN](./media/howto-mfa-nps-extension-vpn/image3.png)

3. Na janela **Selecionar tipo de conexões de rede virtual privada ou dial-up** , selecione **conexões de rede virtual privada**e, em seguida, selecione **Avançar**.

    ![Configurar conexões de rede virtual privada](./media/howto-mfa-nps-extension-vpn/image4.png)

4. Na janela **especificar servidor dial-up ou VPN** , selecione **Adicionar**.

5. Na janela **novo cliente RADIUS** , forneça um nome amigável, insira o nome ou endereço IP que possa ser resolvido do servidor VPN e, em seguida, insira uma senha de segredo compartilhado. Torne a senha secreta compartilhada longa e complexa. Registre-o, pois você precisará dele na próxima seção.

    ![Criar uma nova janela do cliente RADIUS](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Selecione **OK**e, em seguida, selecione **Avançar**.

7. Na janela **Configurar métodos de autenticação** , aceite a seleção padrão (**autenticação criptografada da Microsoft versão 2 [MS-CHAPv2])** ou escolha outra opção e selecione **Avançar**.

    > [!NOTE]
    > Se você configurar o protocolo EAP (Extensible Authentication Protocol), deverá usar o protocolo de autenticação de handshake de desafio da Microsoft (CHAPv2) ou o protocolo PEAP (Protected extensível Authentication Protocol). Não há suporte para nenhum outro EAP.

8. Na janela **especificar grupos de usuários** , selecione **Adicionar**e, em seguida, selecione um grupo apropriado. Se não houver nenhum grupo, deixe a seleção em branco para conceder acesso a todos os usuários.

    ![Especificar a janela grupos de usuários para permitir ou negar acesso](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Selecione **Seguinte**.

10. Na janela **especificar filtros IP** , selecione **Avançar**.

11. Na janela **especificar configurações de criptografia** , aceite as configurações padrão e, em seguida, selecione **Avançar**.

    ![A janela especificar configurações de criptografia](./media/howto-mfa-nps-extension-vpn/image8.png)

12. Na janela **especificar um nome de realm** , deixe o nome do realm em branco, aceite a configuração padrão e, em seguida, selecione **Avançar**.

    ![A janela especificar um nome de realm](./media/howto-mfa-nps-extension-vpn/image9.png)

13. Na janela **concluindo a nova conexão discada ou rede virtual privada e clientes RADIUS** , selecione **concluir**.

    ![Janela de configuração concluída](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Verificar a configuração do RADIUS

Esta seção detalha a configuração que você criou usando o assistente.

1. No servidor de políticas de rede, no console do NPS (local), expanda **clientes RADIUS**e selecione **clientes RADIUS**.

2. No painel de detalhes, clique com o botão direito do mouse no cliente RADIUS que você criou e selecione **Propriedades**. As propriedades do seu cliente RADIUS (o servidor VPN) devem ser semelhantes às mostradas aqui:

    ![Verificar as propriedades e configurações de VPN](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Selecione **Cancelar**.

4. No servidor de políticas de rede, no console do NPS (local), expanda **políticas**e selecione **políticas de solicitação de conexão**. A política de conexões VPN é exibida conforme mostrado na imagem a seguir:

    ![Política de solicitação de conexão mostrando política de conexão VPN](./media/howto-mfa-nps-extension-vpn/image12.png)

5. Em **políticas**, selecione **políticas de rede**. Você deve ver uma política de conexões de VPN (rede virtual privada) que se assemelha à política mostrada na imagem a seguir:

    ![Políticas de rede mostrando a política de conexões de rede virtual privada](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Configurar o servidor VPN para usar a autenticação RADIUS

Nesta seção, você configura o servidor VPN para usar a autenticação RADIUS. As instruções pressupõem que você tenha uma configuração funcional de um servidor VPN, mas não o configurou para usar a autenticação RADIUS. Depois de configurar o servidor VPN, confirme se sua configuração está funcionando conforme o esperado.

> [!NOTE]
> Se você já tiver uma configuração de servidor VPN em funcionamento que usa a autenticação RADIUS, ignore esta seção.
>

### <a name="configure-authentication-provider"></a>Configurar o provedor de autenticação

1. No servidor VPN, abra Gerenciador do Servidor.

2. Em Gerenciador do Servidor, selecione **ferramentas**e, em seguida, selecione **Roteamento e acesso remoto**.

3. Na janela **Roteamento e acesso remoto** , clique com o botão direito do mouse em **\<nome do servidor > (local)** e selecione **Propriedades**.

4. Na janela **Propriedades do > nome do servidor do\<(local)** , selecione a guia **segurança** .

5. Na guia **segurança** , em **provedor de autenticação**, selecione **autenticação RADIUS**e, em seguida, selecione **Configurar**.

    ![Configurar o provedor de autenticação RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)

6. Na janela **autenticação RADIUS** , selecione **Adicionar**.

7. Na janela **Adicionar servidor RADIUS** , faça o seguinte:

    a. Na caixa **nome do servidor** , digite o nome ou o endereço IP do servidor RADIUS que você configurou na seção anterior.

    b. Para o **segredo compartilhado**, selecione **alterar**e, em seguida, insira a senha de segredo compartilhado que você criou e registrou anteriormente.

    c. Na caixa **tempo limite (segundos)** , insira um valor de **30**.  
    O valor de tempo limite é necessário para permitir tempo suficiente para concluir o segundo fator de autenticação.

    ![Adicionar janela do servidor RADIUS Configurando o tempo limite](./media/howto-mfa-nps-extension-vpn/image16.png)

8. Selecione **OK**.

### <a name="test-vpn-connectivity"></a>Testar conectividade de VPN

Nesta seção, você confirma que o cliente VPN é autenticado e autorizado pelo servidor RADIUS quando tenta se conectar à porta virtual VPN. As instruções pressupõem que você esteja usando o Windows 10 como um cliente VPN.

> [!NOTE]
> Se você já tiver configurado um cliente VPN para se conectar ao servidor VPN e salvar as configurações, poderá ignorar as etapas relacionadas à configuração e salvamento de um objeto de conexão VPN.
>

1. No computador cliente VPN, selecione o botão **Iniciar** e, em seguida, selecione o botão **configurações** .

2. Na janela **configurações do Windows** , selecione **rede & Internet**.

3. Selecione **VPN**.

4. Selecione **Adicionar uma conexão VPN**.

5. Na janela **Adicionar uma conexão VPN** , na caixa **provedor de VPN** , selecione **Windows (interno)** , preencha os campos restantes, conforme apropriado e, em seguida, selecione **salvar**.

    ![A janela "adicionar uma conexão VPN"](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Vá para o **painel de controle**e selecione **central de rede e compartilhamento**.

7. Selecione **alterar configurações do adaptador**.

    ![Central de rede e compartilhamento-alterar configurações do adaptador](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Clique com o botão direito do mouse na conexão de rede VPN e selecione **Propriedades**.

9. Na janela Propriedades de VPN, selecione a guia **segurança** .

10. Na guia **segurança** , verifique se somente **Microsoft CHAP versão 2 (MS-CHAP v2)** está selecionado e, em seguida, selecione **OK**.

    ![A opção "permitir estes protocolos"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Clique com o botão direito do mouse na conexão VPN e selecione **conectar**.

12. Na janela **configurações** , selecione **conectar**.  
    Uma conexão bem-sucedida aparece no log de segurança, no servidor RADIUS, como ID de evento 6272, conforme mostrado aqui:

    ![janela Propriedades de eventos mostrando uma conexão bem-sucedida](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Solucionando problemas de RADIUS

Suponha que sua configuração de VPN estava funcionando antes de você ter configurado o servidor VPN para usar um servidor RADIUS centralizado para autenticação e autorização. Se a configuração estava funcionando, é provável que o problema seja causado por uma configuração incorreta do servidor RADIUS ou pelo uso de um nome de usuário ou senha inválido. Por exemplo, se você usar o sufixo UPN alternativo no nome de usuário, a tentativa de entrada poderá falhar. Use o mesmo nome de conta para obter melhores resultados.

Para solucionar esses problemas, um lugar ideal para começar é examinar os logs de eventos de segurança no servidor RADIUS. Para economizar tempo procurando eventos, você pode usar a exibição personalizada da política de rede baseada em função e do servidor de acesso no Visualizador de Eventos, conforme mostrado aqui. "ID do evento 6273" indica eventos em que o NPS negou acesso a um usuário.

![Visualizador de Eventos mostrando eventos NPAS](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Configurar o multi-factor Authentication

Para obter ajuda para configurar usuários para autenticação multifator, consulte os artigos [planejando uma implantação da autenticação multifator do Azure baseada em nuvem](howto-mfa-getstarted.md#create-conditional-access-policy) e [configurar minha conta para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Instalar e configurar a extensão do NPS

Esta seção fornece instruções para configurar a VPN para usar a MFA para autenticação de cliente com o servidor VPN.

Depois de instalar e configurar a extensão do NPS, toda a autenticação de cliente baseada em RADIUS que é processada por esse servidor é necessária para usar a MFA. Se todos os usuários de VPN não estiverem registrados na autenticação multifator do Azure, você poderá fazer o seguinte:

* Configure outro servidor RADIUS para autenticar usuários que não estão configurados para usar a MFA.

* Crie uma entrada de registro que permita que usuários desafiados forneçam um segundo fator de autenticação se eles estiverem registrados na autenticação multifator do Azure.

Crie um novo valor de cadeia de caracteres chamado _REQUIRE_USER_MATCH em HKLM\SOFTWARE\Microsoft\AzureMfa_e defina o valor como *true* ou *false*.

![A configuração "exigir correspondência de usuário"](./media/howto-mfa-nps-extension-vpn/image34.png)

Se o valor for definido como *true* ou estiver em branco, todas as solicitações de autenticação estarão sujeitas a um desafio de MFA. Se o valor for definido como *false*, os desafios de MFA serão emitidos somente para usuários registrados na autenticação multifator do Azure. Use a configuração *false* somente em ambientes de teste ou de produção durante um período de integração.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Obter a ID do GUID de Azure Active Directory

Como parte da configuração da extensão do NPS, você deve fornecer credenciais de administrador e a ID do seu locatário do Azure AD. Obtenha a ID fazendo o seguinte:

1. Entre no [portal do Azure](https://portal.azure.com) como o administrador global do locatário do Azure.

2. No painel esquerdo, selecione o botão **Azure Active Directory** .

3. Selecione **Propriedades**.

4. Para copiar sua ID do Azure AD, selecione o botão **copiar** .

    ![ID de diretório do Azure AD no portal do Azure](./media/howto-mfa-nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Instalar a extensão NPS

A extensão NPS deve ser instalada em um servidor que tenha a função de serviços de acesso e política de rede instalada e que funcione como o servidor RADIUS em seu design. *Não* instale a extensão NPS no servidor VPN.

1. Baixe a extensão NPS do [centro de download da Microsoft](https://aka.ms/npsmfa).

2. Copie o arquivo executável da instalação (*NpsExtnForAzureMfaInstaller. exe*) para o servidor NPS.

3. No servidor NPS, clique duas vezes em **NpsExtnForAzureMfaInstaller. exe** e, se solicitado, selecione **executar**.

4. Na janela **extensão do NPS para a instalação do Azure MFA** , examine os termos de licença de software, marque a caixa de seleção **eu concordo com os termos e condições da licença** e selecione **instalar**.

    ![A janela "extensão do NPS para a instalação do Azure MFA"](./media/howto-mfa-nps-extension-vpn/image36.png)

5. Na janela **extensão do NPS para a instalação do Azure MFA** , selecione **fechar**.  

    ![A janela de confirmação "instalação bem-sucedida"](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Configurar certificados para uso com a extensão NPS usando um script do PowerShell

Para garantir comunicações e garantias seguras, configure certificados para uso pela extensão do NPS. Os componentes NPS incluem um script do Windows PowerShell que configura um certificado autoassinado para utilização com NPS.

O script realiza as seguintes ações:

* Cria um certificado autoassinado.
* Associa a chave pública do certificado à entidade de serviço no Azure AD.
* Armazena o certificado no repositório do computador local.
* Concede ao usuário de rede acesso à chave privada do certificado.
* Reinicia o serviço NPS.

Se você quiser usar seus próprios certificados, deverá associar a chave pública do seu certificado à entidade de serviço no Azure AD e assim por diante.

Para usar o script, forneça a extensão com suas credenciais de Azure Active Directory administrativas e a ID de locatário Azure Active Directory que você copiou anteriormente. Execute o script em cada servidor NPS onde você instala a extensão NPS.

1. Execute o Windows PowerShell como administrador.

2. No prompt de comando do PowerShell, digite **CD "C:\Program Files\Microsoft\AzureMfa\Config"** e, em seguida, selecione Enter.

3. No próximo prompt de comando, digite **.\AzureMfaNpsExtnConfigSetup.ps1**e, em seguida, selecione Enter. O script verifica se o módulo PowerShell do Azure AD está instalado. Se não estiver instalado, o script instalará o módulo para você.

    ![Executando o script de configuração AzureMfsNpsExtnConfigSetup. ps1](./media/howto-mfa-nps-extension-vpn/image38.png)

    Depois que o script verifica a instalação do módulo do PowerShell, ele exibe a janela de entrada do módulo Azure Active Directory PowerShell.

4. Insira suas credenciais e senha de administrador do Azure AD e, em seguida, selecione **entrar**.

    ![Autenticar no Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. No prompt de comando, Cole a ID do locatário que você copiou anteriormente e, em seguida, selecione Enter.

    ![Insira a ID de diretório do Azure AD copiada antes](./media/howto-mfa-nps-extension-vpn/image40.png)

    O script cria um certificado autoassinado e realiza outras alterações de configuração. A saída é parecida com a seguinte imagem:

    ![Janela do PowerShell mostrando o certificado autoassinado](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Reinicialize o servidor.

### <a name="verify-the-configuration"></a>Verificar a configuração

Para verificar a configuração, você deve estabelecer uma nova conexão VPN com o servidor VPN. Depois de inserir com êxito suas credenciais para autenticação primária, a conexão VPN aguardará que a autenticação secundária seja bem-sucedida antes que a conexão seja estabelecida, conforme mostrado abaixo.

![A janela VPN de configurações do Windows](./media/howto-mfa-nps-extension-vpn/image42.png)

Se você se autenticar com êxito com o método de verificação secundário configurado anteriormente na MFA do Azure, você está conectado ao recurso. No entanto, se a autenticação secundária não for bem-sucedida, o acesso ao recurso será negado.

No exemplo a seguir, o aplicativo Microsoft Authenticator em um Windows Phone fornece a autenticação secundária:

![Exemplo de prompt de MFA em Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

Depois de autenticado com êxito usando o método secundário, você terá acesso à porta virtual no servidor VPN. Como você era solicitado a usar um método de autenticação secundário usando um aplicativo móvel em um dispositivo confiável, o processo de entrada é mais seguro do que se estivesse usando apenas uma combinação de nome de usuário e senha.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Exibir logs de Visualizador de Eventos para eventos de entrada bem-sucedidos

Para exibir eventos de entrada bem-sucedidos nos logs do Windows Visualizador de Eventos consulte o log de segurança do Windows, no servidor NPS, digitando o seguinte comando do PowerShell:

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Visualizador de Eventos de segurança do PowerShell](./media/howto-mfa-nps-extension-vpn/image44.png)

Você também pode exibir o log de segurança ou a exibição personalizada de política de rede e serviços de acesso, conforme mostrado aqui:

![Exemplo de log do servidor de políticas de rede](./media/howto-mfa-nps-extension-vpn/image45.png)

No servidor em que você instalou a extensão NPS para a autenticação multifator do Azure, você pode encontrar Visualizador de Eventos logs de aplicativo que são específicos para a extensão em *aplicativos e serviços Logs\Microsoft\AzureMfa*.

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Exemplo Visualizador de Eventos painel de logs do AuthZ](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Se a configuração não estiver funcionando conforme o esperado, inicie a solução de problemas verificando se o usuário está configurado para usar a MFA. Peça ao usuário para se conectar ao [portal do Azure](https://portal.azure.com). Se o usuário for solicitado a fornecer a autenticação secundária e puder se autenticar com êxito, você poderá eliminar uma configuração incorreta da MFA como um problema.

Se a MFA estiver funcionando para o usuário, examine os logs de Visualizador de Eventos relevantes. Os logs incluem o evento de segurança, o gateway operacional e os logs da autenticação multifator do Azure que são discutidos na seção anterior.

Um exemplo de um log de segurança que exibe um evento de entrada com falha (ID do evento 6273) é mostrado aqui:

![Log de segurança mostrando um evento de entrada com falha](./media/howto-mfa-nps-extension-vpn/image47.png)

Um evento relacionado do log da autenticação multifator do Azure é mostrado aqui:

![Logs da autenticação multifator do Azure](./media/howto-mfa-nps-extension-vpn/image48.png)

Para solucionar problemas avançados, consulte os arquivos de log do formato de banco de dados NPS em que o serviço NPS está instalado. Os arquivos de log são criados na pasta _%systemroot%\System32\Logs_ como arquivos de texto delimitados por vírgula. Para obter uma descrição dos arquivos de log, consulte [interpretar arquivos de log de formato de banco de dados NPS](https://technet.microsoft.com/library/cc771748.aspx).

As entradas nesses arquivos de log são difíceis de interpretar, a menos que você as exporte para uma planilha ou um banco de dados. Você pode encontrar muitas ferramentas de análise do serviço de autenticação da Internet (IAS) online para ajudá-lo a interpretar os arquivos de log. A saída de um desses [aplicativos Shareware](https://www.deepsoftware.com/iasviewer) que pode ser baixado é mostrada aqui:

![Analisador de IAS do aplicativo shareware de exemplo](./media/howto-mfa-nps-extension-vpn/image49.png)

Para solucionar problemas adicionais, você pode usar um analisador de protocolo como o Wireshark ou [o Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). A imagem a seguir do Wireshark mostra as mensagens RADIUS entre o servidor VPN e o NPS.

![Analisador de mensagem da Microsoft mostrando tráfego filtrado](./media/howto-mfa-nps-extension-vpn/image50.png)

Para obter mais informações, consulte [integrar sua infraestrutura de NPS existente com a autenticação multifator do Azure](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Passos seguintes

[Obter a autenticação multifator do Azure](concept-mfa-licensing.md)

[Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS](howto-mfaserver-nps-rdg.md)

[Integrar os diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
