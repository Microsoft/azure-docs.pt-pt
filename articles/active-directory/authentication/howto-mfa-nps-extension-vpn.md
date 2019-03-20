---
title: Integrar o VPN com o MFA do Azure ao utilizar a extensão de servidor de políticas de rede | Documentos da Microsoft
description: Integre a sua infraestrutura VPN com o MFA do Azure ao utilizar a extensão de servidor de políticas de rede para o Microsoft Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22b035b325582cf4a12ff10a9abbedb8837229b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58117885"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrar a sua infraestrutura VPN com o MFA do Azure ao utilizar a extensão de servidor de políticas de rede para o Azure

## <a name="overview"></a>Descrição geral

A extensão de servidor de políticas de rede (NPS) para o Azure permite que as organizações a salvaguardar a autenticação de cliente de Remote Authentication Dial-In User Service (RADIUS) com base na cloud [Azure multi-factor Authentication (MFA)](howto-mfaserver-nps-rdg.md), que fornece verificação de dois passos.

Este artigo fornece instruções para integrar a infraestrutura NPS com a MFA, utilizando a extensão NPS para Azure. Este processo permite a verificação de dois passos segura para os utilizadores que tentarem ligar à sua rede com uma VPN. 

Serviços de acesso e política de rede proporciona às organizações a capacidade de:

* Atribua um local central para o gerenciamento e controle de pedidos de rede para especificar:

  * Quem pode se conectar 
    
  * Os tempos de ligações de dia são permitidos 
    
  * A duração de ligações
    
  * O nível de segurança que os clientes devem utilizar para ligar

    Em vez de especificar políticas em cada servidor VPN ou Gateway de ambiente de trabalho remoto, fazê-lo Depois de estarem num local central. O protocolo RADIUS é utilizado para fornecer autenticação centralizada, autorização e contabilidade (AAA). 

* Estabelecer e impor políticas de estado de funcionamento de cliente de proteção de acesso de rede (NAP) que determinam se os dispositivos recebem acesso restrito ou irrestrito aos recursos de rede.

* Fornecer uma forma para impor a autenticação e autorização de acesso 802.1 pontos de acesso sem fio com capacidade de x e comutadores de Ethernet.   
  Para obter mais informações, consulte [servidor de políticas de rede](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Para melhorar a segurança e fornecer um alto nível de conformidade, as organizações podem integrar o NPS com o Azure multi-factor Authentication para se certificar de que os utilizadores utilizar a verificação de dois passos para ligar à porta virtual no servidor VPN. Para os utilizadores lhe ser concedido, devem fornecer o nome de utilizador e a combinação de palavra-passe e outras informações que elas controlam. Estas informações tem de ser fidedigno e não facilmente duplicadas. Ele pode incluir um número de telefone celular, um número fixo ou uma aplicação num dispositivo móvel.

Antes da disponibilidade da extensão NPS para Azure, os clientes que desejavam implementar a verificação de dois passos para integrado NPS e ambientes de MFA tinham que configurar e manter um servidor MFA separado num ambiente no local. Este tipo de autenticação é oferecido pelo Gateway de ambiente de trabalho remoto e servidor de autenticação do multi-factor do Azure com o RADIUS.

Com a extensão NPS para Azure, as organizações podem proteger a autenticação de cliente RADIUS ao implementar uma solução MFA no local com base ou uma solução MFA com base na cloud.
 
## <a name="authentication-flow"></a>Fluxo de autenticação
Quando os utilizadores ligam-se para uma porta virtual num servidor VPN, devem primeiro autenticar utilizando uma variedade de protocolos. Os protocolos de permitem a utilização de uma combinação de nome de utilizador e palavra-passe e métodos de autenticação baseada em certificados. 

Além de autenticar e verificar a sua identidade, os utilizadores tem de ter as permissões de acesso telefónico adequadas. Em implementações simples, as permissões de acesso telefónico que permitem o acesso são definidas diretamente em objetos de utilizador do Active Directory. 

![Propriedades do utilizador](./media/howto-mfa-nps-extension-vpn/image1.png)

Em implementações simples, cada servidor VPN concede ou nega o acesso com base nas políticas que estão definidas em cada servidor VPN local.

Em implementações maiores e mais escalonáveis, as políticas que concedem ou negarem o acesso VPN estão centralizadas em servidores RADIUS. Nestes casos, o servidor VPN atua como um servidor de acesso (cliente RADIUS), que encaminha pedidos de ligação e mensagens de conta para um servidor RADIUS. Para ligar à porta virtual no servidor VPN, os utilizadores têm de ser autenticados e cumprem as condições definidas centralmente em servidores RADIUS. 

Quando a extensão NPS para Azure está integrada com o NPS, um fluxo de autenticação com êxito resulta, da seguinte forma:

1. No servidor VPN ao recebe um pedido de autenticação de um utilizador VPN que inclui o nome de utilizador e palavra-passe para ligar a um recurso, como uma sessão de ambiente de trabalho remoto. 

2. Atuando como um cliente RADIUS, o servidor VPN converte o pedido para um raio *pedido de acesso* da mensagem e envia-os (com uma palavra-passe encriptada) para o servidor RADIUS onde está instalada a extensão NPS. 

3. A combinação de nome de utilizador e palavra-passe é verificar no Active Directory. Se o nome de utilizador ou palavra-passe está incorreta, o servidor de RADIUS envia uma *rejeitar acesso* mensagem. 

4. Se forem cumpridos todas as condições, conforme especificado no pedido de ligação de NPS e políticas de rede (por exemplo, a hora do dia ou grupo de restrições de associação), a extensão NPS aciona um pedido de autenticação secundária com o Azure multi-factor Authentication. 

5. O Azure multi-factor Authentication se comunica com o Azure Active Directory, obtém os detalhes do utilizador e efetua a autenticação secundária através do método que está configurado pelo utilizador (célula telefonema, mensagem de texto ou aplicação móvel). 

6. Quando a submissão da MFA for bem-sucedida, o Azure multi-factor Authentication se comunica com o resultado para a extensão NPS.

7. Depois da tentativa de ligação é autenticada e autorizada, o NPS em que a extensão é instalada envia um raio *aceitar o acesso* mensagem para o servidor VPN (cliente RADIUS).

8. O utilizador é concedido acesso à porta virtual no servidor VPN e estabelece um túnel VPN encriptado.

## <a name="prerequisites"></a>Pré-requisitos
Esta secção fornece detalhes sobre os pré-requisitos que devem ser concluídos antes de MFA pode ser integrado com o Gateway de ambiente de trabalho remoto. Antes de começar, tem de ter os seguintes pré-requisitos no local:

* Infraestrutura de VPN
* Função de política de rede e serviços de acesso
* Licença de multi-factor Authentication do Azure
* Software Windows Server
* Bibliotecas
* Azure Active Directory (Azure AD) sincronizadas com a no local do Active Directory 
* ID de GUID do Azure Active Directory

### <a name="vpn-infrastructure"></a>Infraestrutura de VPN
Este artigo pressupõe que tem uma infraestrutura VPN de trabalho que utiliza o Microsoft Windows Server 2016 e que seu servidor VPN atualmente não está configurado para pedidos de ligação direta para um servidor RADIUS. O artigo, vai configurar a infraestrutura VPN para utilizar um servidor RADIUS central.

Se não tiver uma infraestrutura VPN de trabalho no local, pode criar rapidamente uma ao seguir as orientações em vários tutoriais de configuração VPN que pode encontrar na Microsoft e sites de terceiros. 
            
### <a name="the-network-policy-and-access-services-role"></a>A função Serviços de acesso e política de rede

Serviços de acesso e política de rede fornece a funcionalidade de servidor e cliente RADIUS. Este artigo pressupõe que instalou a função Serviços de acesso e política de rede num servidor membro ou controlador de domínio no seu ambiente. Neste guia, configure o RADIUS para uma configuração de VPN. Instalar a função Serviços de acesso e política de rede num servidor *diferente de* seu servidor VPN.

Para obter informações sobre como instalar a função Serviços de acesso e política de rede do serviço Windows Server 2012 ou superior, veja [instalar um servidor de políticas de estado de funcionamento de NAP](https://technet.microsoft.com/library/dd296890.aspx). NAP foi preterido no Windows Server 2016. Para obter uma descrição de melhores práticas para o NPS, incluindo a recomendação para instalar NPS num controlador de domínio, consulte [melhores práticas para NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Licença do MFA do Azure

É necessária uma licença para o Azure multi-factor Authentication e está disponível através do Azure AD Premium, Enterprise Mobility + Security ou uma licença autónoma do multi-factor Authentication. Baseado no consumo licenças do MFA do Azure, como por utilizador ou por licenças de autenticação não são compatíveis com a extensão NPS. Para obter mais informações, consulte [como obter o Azure multi-factor Authentication](concept-mfa-licensing.md). Para fins de teste, pode utilizar uma subscrição de avaliação.

### <a name="windows-server-software"></a>Software Windows Server

A extensão NPS requer o Windows Server 2008 R2 SP1 ou posterior, com a função Serviços de acesso e política de rede instalados. Todos os passos neste guia foram realizados com o Windows Server 2016.

### <a name="libraries"></a>Bibliotecas

As seguintes bibliotecas são instaladas automaticamente com a extensão NPS:

-   [Visual C++ Redistributable Packages para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft módulo Azure Active Directory para Windows PowerShell versão 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Se o módulo de PowerShell de diretório Active Directory do Microsoft Azure já não estiver presente, é instalado com um script de configuração que executar como parte do processo de configuração. Não é necessário para instalar o módulo antes do tempo, se não estiver já instalado.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>O Azure Active Directory sincronizado com o Active Directory no local 

Para utilizar a extensão NPS, os utilizadores no local tem de ser sincronizados com o Azure Active Directory e ativados para a MFA. Este guia assume que os utilizadores no local são sincronizados com o Azure Active Directory através do Azure AD Connect. Abaixo, são fornecidas instruções para permitir que os utilizadores para a MFA.

Para obter informações sobre o Azure AD Connect, consulte [integrar seus diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md). 

### <a name="azure-active-directory-guid-id"></a>ID de GUID do Azure Active Directory 

Para instalar a extensão NPS, precisa saber o GUID do Azure Active Directory. Na secção seguinte, são fornecidas instruções para localizar o GUID do Azure Active Directory.

## <a name="configure-radius-for-vpn-connections"></a>Configurar o RADIUS para ligações VPN

Se instalou a função NPS num servidor membro, terá de configurá-lo para autenticar e autorizar o cliente VPN que ligações de VPN de pedidos. 

Esta secção assume que instalou a função de serviços de acesso e política de rede, mas não tiver configurado-lo para utilização na sua infraestrutura.

> [!NOTE]
> Se já tiver um servidor VPN de trabalho que utiliza um servidor centralizado de RADIUS para autenticação, pode ignorar esta secção.
>

### <a name="register-server-in-active-directory"></a>Registar o servidor no Active Directory
Para funcionar corretamente neste cenário, o servidor NPS tem de ser registado no Active Directory.

1. Abra o Gestor de servidor.

2. No Gestor de servidor, selecione **ferramentas**e, em seguida, selecione **servidor de políticas de rede**. 

3. Na consola do servidor de políticas de rede, clique com botão direito **NPS (Local)** e, em seguida, selecione **server registar-se no Active Directory**. Selecione **OK** duas vezes.

    ![Servidor de políticas de rede](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Deixe a consola aberta para o próximo procedimento.

### <a name="use-wizard-to-configure-the-radius-server"></a>Utilizar o Assistente para configurar o servidor RADIUS
Pode usar um padrão (baseada em Assistente) ou avançadas a opção de configuração para configurar o servidor RADIUS. Esta secção assume que está a utilizar a opção de configuração padrão baseada em Assistente.

1. Na consola do servidor de políticas de rede, selecione **NPS (Local)**.

2. Sob **configuração padrão**, selecione **servidor RADIUS para ligações VPN ou de marcação telefónica**e, em seguida, selecione **configurar VPN ou Dial-Up**.

    ![Configurar a VPN](./media/howto-mfa-nps-extension-vpn/image3.png)

3. Na **selecione Dial-up ou tipo de ligações de rede privada Virtual** janela, selecione **ligações de rede privada Virtual**e, em seguida, selecione **seguinte**.

    ![Rede privada virtual](./media/howto-mfa-nps-extension-vpn/image4.png)

4. Na **especificar Dial-Up ou servidor VPN** janela, selecione **Add**.

5. Na **cliente RADIUS novo** janela, forneça um nome amigável, introduza o nome resolúvel ou o endereço IP do servidor VPN e, em seguida, introduza uma palavra-passe secreta partilhada. Tornar a senha secreta compartilhada longa e complexa. Registe-lo, porque ele será necessário na próxima seção.

    ![Novo cliente RADIUS](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Selecione **OK**e, em seguida, selecione **próxima**.

7. Na **configurar métodos de autenticação** janela, aceite a seleção predefinida (**Microsoft encriptados autenticação versão 2 [MS-CHAPv2])** ou escolha outra opção e selecione **seguinte** .

    > [!NOTE]
    > Se configurar o protocolo de autenticação extensível (EAP), tem de utilizar o Microsoft Challenge Handshake Authentication Protocol (CHAPv2) ou autenticação protocolo PEAP (Protected Extensible). Nenhum outro tipo de EAP é suportado.
 
8. Na **especificar grupos de utilizadores** janela, selecione **Add**e, em seguida, selecione um grupo adequado. Se nenhum grupo de existir, deixe a seleção em branco para conceder acesso a todos os utilizadores.

    ![A janela de especificar grupos de utilizadores](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Selecione **Seguinte**.

10. Na **especificar filtros de IP** janela, selecione **próxima**.

11. Na **especificar as definições de encriptação** janela, aceite as predefinições e, em seguida, selecione **próxima**.

    ![A janela de especificar as definições de encriptação](./media/howto-mfa-nps-extension-vpn/image8.png)

12. Na **Especifique um nome de Realm** janela, deixe o nome do realm em branco, aceite a predefinição e, em seguida, selecione **próxima**.

    ![Especificar uma janela de nome do Realm](./media/howto-mfa-nps-extension-vpn/image9.png)

13. Na **clientes concluir novo Dial-up ou ligações de rede privada Virtual e RADIUS** janela, selecione **concluir**.

    ![A janela de "Concluir novo Dial-up ou ligações de rede privada Virtual e RADIUS de clientes"](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Verificar a configuração RADIUS
Esta secção fornece detalhes sobre a configuração que criou utilizando o assistente.

1. No servidor de política de rede, na consola do NPS (local), expanda **clientes RADIUS**e, em seguida, selecione **clientes RADIUS**.

2. No painel de detalhes, clique com botão direito do cliente RADIUS que criou e, em seguida, selecione **propriedades**. As propriedades do cliente RADIUS (o servidor VPN) devem ser como as mostradas aqui:

    ![Propriedades VPN](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Selecione **Cancelar**.

4. No servidor de política de rede, na consola do NPS (local), expanda **políticas**e, em seguida, selecione **políticas de pedido de ligação**. A política de ligações VPN é apresentada, conforme mostrado na imagem seguinte:

    ![Pedidos de ligação](./media/howto-mfa-nps-extension-vpn/image12.png)

5. Sob **políticas**, selecione **políticas de rede**. Deverá ver uma política de ligações de rede privada Virtual (VPN) que se assemelha a política mostrada na imagem seguinte:

    ![Políticas de rede](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Configurar o seu servidor VPN para utilizar a autenticação RADIUS
Nesta seção, configurar seu servidor VPN para utilizar a autenticação RADIUS. As instruções partem do princípio de que tem uma configuração de trabalho de um servidor VPN, mas não tiver configurado para utilizar a autenticação RADIUS. Depois de configurar o servidor VPN, confirme que a configuração está a funcionar conforme esperado.

> [!NOTE]
> Se já tiver uma configuração de servidor VPN de trabalho que utiliza a autenticação RADIUS, pode ignorar esta secção.
>

### <a name="configure-authentication-provider"></a>Configurar o fornecedor de autenticação
1. No servidor VPN, abra o Gestor de servidor.

2. No Gestor de servidor, selecione **ferramentas**e, em seguida, selecione **encaminhamento e acesso remoto**.

3. Na **encaminhamento e acesso remoto** janela, clique com botão direito  **\<nome do servidor > (local)** e, em seguida, selecione **propriedades**.

    ![A janela de encaminhamento e acesso remoto](./media/howto-mfa-nps-extension-vpn/image14.png)
 
4. Na  **\<nome do servidor > propriedades (local)** janela, selecione o **segurança** separador. 

5. Sobre o **Security** separador, em **fornecedor de autenticação**, selecione **autenticação RADIUS**e, em seguida, selecione **configurar**.

    ![Autenticação RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)
 
6. Na **autenticação RADIUS** janela, selecione **Add**.

7. Na **Adicionar servidor RADIUS** janela, efetue o seguinte procedimento:

    a. Na **nome do servidor** , introduza o nome ou endereço IP do servidor RADIUS que configurou na secção anterior.

    b. Para o **segredo partilhado**, selecione **alteração**e, em seguida, introduza a senha secreta compartilhada que criou e registrado anteriormente.

    c. Na **tempo limite (segundos)** caixa, selecione um valor **30** através de **60**.  
    O valor de tempo limite é necessário para permitir tempo suficiente concluir o segundo fator de autenticação.
 
    ![A janela Adicionar servidor RADIUS](./media/howto-mfa-nps-extension-vpn/image16.png)
 
8. Selecione **OK**.

### <a name="test-vpn-connectivity"></a>Testar a conectividade VPN
Nesta secção, confirme que o cliente VPN é autenticado e autorizado pelo servidor RADIUS, quando está tentando se conectar à porta virtual VPN. As instruções partem do princípio de que está a utilizar o Windows 10 como um cliente VPN. 

> [!NOTE]
> Se já configurou um cliente VPN para se ligar ao servidor VPN e guardar as definições, pode ignorar as etapas relacionadas com a configuração e a guardar um objeto de conexão de VPN.
>

1. No seu computador de cliente VPN, selecione o **começar** e, em seguida, selecione a **definições** botão.

2. Na **definições do Windows** janela, selecione **rede e Internet**.

3. Selecione **VPN**.

4. Selecione **adicionar uma ligação VPN**.

5. Na **adicionar uma ligação de VPN** janela, na **fornecedor de VPN** caixa, selecione **Windows (incorporados)**, preencha os campos restantes, conforme adequado e, em seguida, selecione **Guardar**. 

    ![A janela "Adicionar uma ligação VPN"](./media/howto-mfa-nps-extension-vpn/image17.png)
 
6. Aceda a **painel de controlo**e, em seguida, selecione **Centro de partilha de rede e**.

7. Selecione **alterar as definições do adaptador**.

    ![Alterar definições do adaptador](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Com o botão direito a ligação de rede VPN e, em seguida, selecione **propriedades**. 

    ![Propriedades de rede VPN](./media/howto-mfa-nps-extension-vpn/image19.png)

9. Na janela de propriedades de VPN, selecione o **segurança** separador. 

10. Sobre o **Security** separador, certifique-se de que apenas **Microsoft CHAP Version 2 (MS-CHAP v2)** está selecionado e, em seguida, selecione **OK**.

    ![A opção "Permitir que esses protocolos"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. A ligação VPN com o botão direito e, em seguida, selecione **Connect**.

12. Na **configurações** janela, selecione **Connect**.  
    É apresentada uma ligação com êxito no registo de segurança no servidor RADIUS que 6272 de ID de evento, como mostrado aqui:

    ![A janela de propriedades do evento](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Resolução de problemas de RADIUS

Partem do princípio de que a configuração de VPN estava funcionando antes de que configurou o servidor VPN para utilizar um servidor centralizado de RADIUS para autenticação e autorização. Se a configuração estava funcionando, é provável que o problema é causado por uma configuração incorreta do servidor RADIUS ou o uso de um nome de utilizador inválido ou a palavra-passe. Por exemplo, se usar o sufixo UPN alternativo o nome de utilizador, a tentativa de início de sessão poderá falhar. Utilize o mesmo nome de conta para obter melhores resultados. 

Para resolver estes problemas, o lugar ideal para começar é examinar os registos de eventos de segurança no servidor de RADIUS. Para economizar tempo pesquisando os eventos, pode utilizar a com base na função servidor de acesso e diretiva de rede vista personalizada no Visualizador de eventos, como mostrado aqui. "" ID de evento 6273 indica onde o NPS impedido de aceder a um utilizador de eventos. 

![Visualizador de Eventos](./media/howto-mfa-nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Configurar o multi-factor Authentication

Para obter ajuda a configurar utilizadores de multi-factor Authentication, consulte os artigos [como requerer verificação de dois passos para um utilizador ou grupo](howto-mfa-userstates.md) e [configurar a minha conta para verificação de dois passos](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Instalar e configurar a extensão NPS

Esta secção fornece instruções para configurar a VPN para utilizar a MFA para a autenticação de cliente com o servidor VPN.

Depois de instalar e configurar a extensão NPS, toda a autenticação baseada em RADIUS cliente que é processado por este servidor é necessário para utilizar a MFA. Se todos os seus utilizadores VPN não estão inscritos no multi-factor Authentication do Azure, pode efetuar um dos seguintes procedimentos:

* Configure outro servidor RADIUS para autenticar os utilizadores que não estão configurados para utilizar a MFA. 

* Crie uma entrada de registo que permite aos utilizadores desafiados a fornecer um segundo fator de autenticação, se estiverem inscritos no Azure multi-factor Authentication. 

Criar um novo valor de cadeia denominado _REQUIRE_USER_MATCH no HKLM\SOFTWARE\Microsoft\AzureMfa_e defina o valor como *True* ou *False*. 

![A definição de "Exigir correspondência do utilizador"](./media/howto-mfa-nps-extension-vpn/image34.png)
 
Se o valor é definido como *True* ou está em branco, todos os pedidos de autenticação estão sujeitos a um desafio MFA. Se o valor é definido como *False*, MFA desafios são emitidos apenas para os utilizadores que estão inscritos no Azure multi-factor Authentication. Utilize o *False* definição apenas no teste ou em ambientes de produção durante um período de integração.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Obter o ID de GUID do Azure Active Directory

Como parte da configuração da extensão NPS, é necessário fornecer credenciais de administrador e o ID de inquilino do Azure AD. Obter o ID, fazendo o seguinte:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como administrador global do inquilino do Azure.

2. No painel esquerdo, selecione o **do Azure Active Directory** botão.

3. Selecione **propriedades**.

4. Para copiar o ID do Azure AD, selecione o **cópia** botão.
 
    ![O ID do Azure AD](./media/howto-mfa-nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Instalar a extensão NPS
A extensão NPS deve ser instalada num servidor que tenha a política de rede e a função de serviços de acesso instalada e que funciona como servidor RADIUS no seu design. Fazer *não* instale a extensão NPS no servidor do ambiente de trabalho remoto.

1. Transfira a extensão NPS da [Microsoft Download Center](https://aka.ms/npsmfa). 

2. Copie o ficheiro executável do programa de configuração (*NpsExtnForAzureMfaInstaller.exe*) para o servidor NPS.

3. No servidor NPS, faça duplo clique em **NpsExtnForAzureMfaInstaller.exe** e, se lhe for pedido, selecione **executar**.

4. Na **extensão de NPS para o Azure MFA configuração** janela, reveja os termos de licenciamento de software, selecione a **concordo com os termos de licenciamento e condições** caixa de verificação e, em seguida, selecione **instalar**.

    ![A "NPS extensão para o Azure MFA" janela de configuração](./media/howto-mfa-nps-extension-vpn/image36.png)
 
5. Na **extensão de NPS para o Azure MFA configuração** janela, selecione **fechar**.  

    ![A janela de confirmação "Configuração concluída com êxito"](./media/howto-mfa-nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Configurar certificados para utilização com a extensão NPS, utilizando um script do PowerShell
Para garantir a proteger as comunicações e garantia, configure certificados para utilização pela extensão de NPS. Os componentes NPS incluem um script do Windows PowerShell que configura um certificado autoassinado para utilização com NPS. 

O script realiza as seguintes ações:

* Cria um certificado autoassinado.
* Associa a chave pública do certificado para o serviço principal no Azure AD.
* Armazena o certificado no arquivo do computador local.
* Concede o acesso de utilizador de rede a chave privada do certificado.
* Reinicia o serviço NPS.

Se desejar usar seus próprios certificados, tem de associar a chave pública do certificado com o principal de serviço no Azure AD e assim por diante.

Para utilizar o script, forneça a extensão com suas credenciais administrativas do Azure Active Directory e o ID de inquilino do Azure Active Directory que copiou anteriormente. Execute o script em cada servidor NPS onde instalou a extensão NPS.

1. Execute o Windows PowerShell como administrador.

2. Na linha de comando do PowerShell, introduza **cd "c:\Program Files\Microsoft\AzureMfa\Config"** e, em seguida, selecione Enter.

3. A seguinte linha de comandos, introduza **.\AzureMfsNpsExtnConfigSetup.ps1**e, em seguida, selecione Enter. O script verifica se o módulo Azure AD PowerShell está instalado. Se não estiver instalado, o script instala o módulo para.
 
    ![PowerShell](./media/howto-mfa-nps-extension-vpn/image38.png)
 
    Depois do script verifica a instalação do módulo do PowerShell, ele exibe a Azure Active Directory PowerShell módulo início de sessão janela. 

4. Introduza as suas credenciais de administrador do Azure AD e a palavra-passe e, em seguida, selecione **iniciar sessão**. 
 
    ![A janela de início de sessão do PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)
 
5. No prompt de comando, cole o ID do inquilino que copiou anteriormente e, em seguida, selecione Enter. 

    ![ID do inquilino](./media/howto-mfa-nps-extension-vpn/image40.png)

    O script cria um certificado autoassinado e realiza outras alterações de configuração. O resultado como esse é na imagem seguinte:

    ![Certificado autoassinado](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Reinicie o servidor.

### <a name="verify-the-configuration"></a>Verificar a configuração
Para verificar a configuração, tem de estabelecer uma nova ligação de VPN com o servidor VPN. Após introduzir com êxito as suas credenciais para autenticação primária, a ligação VPN aguarda que a autenticação secundária com êxito antes da ligação é estabelecida, conforme mostrado abaixo. 

![A janela de VPN de definições do Windows](./media/howto-mfa-nps-extension-vpn/image42.png)

Se autenticar com êxito com o método de verificação secundária que configurou anteriormente na MFA do Azure, está ligado ao recurso. No entanto, se a autenticação secundária não for bem-sucedida, for negado o acesso ao recurso. 

No exemplo seguinte, a aplicação Microsoft Authenticator num Windows Phone fornece a autenticação secundária:

![Verificar conta](./media/howto-mfa-nps-extension-vpn/image43.png)

Depois de autenticado com êxito ao utilizar o método secundário, são concedidos acesso à porta virtual no servidor VPN. Uma vez que era necessário para utilizar um método de autenticação secundária com uma aplicação móvel num dispositivo fidedigno, o processo de início de sessão é mais seguro do que se ele estivesse usando apenas uma combinação de nome de utilizador e palavra-passe.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Ver registos do Visualizador de eventos para eventos de início de sessão bem-sucedidos
Para ver eventos de início de sessão bem-sucedidos nos registos do Visualizador de eventos do Windows, consulta a segurança do Windows inicie sessão no servidor NPS, introduzindo o seguinte comando do PowerShell:

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

![Visualizador de eventos de segurança do PowerShell](./media/howto-mfa-nps-extension-vpn/image44.png)
 
Também pode ver o registo de segurança ou a vista personalizada serviços de acesso e política de rede, como mostrado aqui:

![Registo do servidor de políticas de rede](./media/howto-mfa-nps-extension-vpn/image45.png)

No servidor onde instalou a extensão NPS para multi-factor Authentication do Azure, pode encontrar registos de aplicações do Visualizador de eventos que são específicos para a extensão na *aplicativos e serviços Logs\Microsoft\AzureMfa*. 

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

![Visualizador de eventos "Número de eventos" painel](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Guia de resolução de problemas
Se a configuração não está a funcionar conforme esperado, iniciar a resolução de problemas a verificar que o utilizador está configurado para utilizar a MFA. Peça ao utilizador ligar para o [portal do Azure](https://portal.azure.com). Se o usuário é solicitado para autenticação secundária e com êxito pode autenticar, pode eliminar uma configuração incorreta do MFA como um problema.

Se esta está a funcionar para o utilizador, consulte os registos do Visualizador de eventos relevantes. Os registos incluem o evento de segurança, Gateway operacional e os registos de multi-factor Authentication discutidos na seção anterior. 

Um exemplo de um registo de segurança que exibe um evento de início de sessão falhado (evento ID 6273) é mostrado aqui:

![Registo de segurança que mostra um evento de início de sessão falhado](./media/howto-mfa-nps-extension-vpn/image47.png)

Um evento relacionado a partir do registo de multi-factor Authentication é mostrado aqui:

![Registos do Azure multi-factor Authentication](./media/howto-mfa-nps-extension-vpn/image48.png)

Para fazer a resolução de problemas avançada, consulte os ficheiros de registo do formato do NPS da base de dados onde o serviço NPS está instalado. Os ficheiros de registo são criados no _%SystemRoot%\System32\Logs_ pasta como arquivos de texto delimitado por vírgulas. Para obter uma descrição dos ficheiros de registo, consulte [interpretar NPS base de dados do formato de ficheiros de registo](https://technet.microsoft.com/library/cc771748.aspx). 

As entradas nestes ficheiros de registo são difíceis de interpretar, a menos que exporte-os para uma folha de cálculo ou uma base de dados. Pode encontrar muitos autenticação serviço Internet (IAS) de análise online de ferramentas para ajudá-lo a interpretar os ficheiros de registo. A saída de um desses que pode ser baixado [aplicativos shareware](https://www.deepsoftware.com/iasviewer) é mostrado aqui: 

![Aplicação de shareware](./media/howto-mfa-nps-extension-vpn/image49.png)

Para fazer a resolução de problemas adicional, pode usar um analisador de protocolo, como o Wireshark ou [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). A imagem seguinte do Wireshark mostra as mensagens RADIUS entre o servidor VPN e o NPS.

![Microsoft Message Analyzer](./media/howto-mfa-nps-extension-vpn/image50.png)

Para obter mais informações, consulte [integrar a infraestrutura NPS existente com o Azure multi-factor Authentication](howto-mfa-nps-extension.md). 

## <a name="next-steps"></a>Passos Seguintes
[Obter a multi-factor Authentication](concept-mfa-licensing.md)

[Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS](howto-mfaserver-nps-rdg.md)

[Integrar os diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

