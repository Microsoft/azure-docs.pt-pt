---
title: VPN com Azure MFA usando a extensão NPS - Azure Ative Directory
description: Integre a sua infraestrutura VPN com o Azure MFA utilizando a extensão do Servidor de Política de Rede para o Microsoft Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7243857db9a3726bb42815ac4c9eef661f52e47
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964728"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integre a sua infraestrutura VPN com o Azure MFA utilizando a extensão do Servidor de Política de Rede para Azure

A extensão do Servidor de Política de Rede (NPS) para o Azure permite que as organizações protejam a autenticação do serviço de acesso ao utilizador (RADIUS) de autenticação remota utilizando [a autenticação multi-factor (MFA)](howto-mfaserver-nps-rdg.md)baseada na nuvem, que proporciona uma verificação em duas etapas.

Este artigo fornece instruções para integrar a infraestrutura NPS com O MFA utilizando a extensão NPS para Azure. Este processo permite uma verificação segura em duas etapas para os utilizadores que tentam ligar-se à sua rede utilizando uma VPN.

A Política de Rede e os Serviços de Acesso conferem às organizações a capacidade de:

* Atribuir uma localização central para a gestão e controlo dos pedidos de rede para especificar:

  * Quem pode ligar

  * Que horas de conexão diurna são permitidas

  * A duração das ligações

  * O nível de segurança que os clientes devem usar para se conectarem

    Em vez de especificar políticas em cada servidor VPN ou Remote Desktop Gateway, faça-o depois de estar em uma localização central. O protocolo RADIUS é utilizado para fornecer autenticação centralizada, Autorização e Contabilidade (AAA).

* Estabeleça e aplique políticas de saúde do cliente de Proteção de Acesso à Rede (NAP) que determinam se os dispositivos são concedidos sem restrições ou acesso restrito aos recursos de rede.

* Forneça uma forma de impor a autenticação e autorização de acesso a pontos de acesso sem fios com capacidade 802.1x e comutadores Ethernet.
  Para obter mais informações, consulte [o Servidor de Política de Rede](/windows-server/networking/technologies/nps/nps-top).

Para aumentar a segurança e fornecer um alto nível de conformidade, as organizações podem integrar NPS com Azure Multi-Factor Authentication para garantir que os utilizadores usam verificação em duas etapas para ligar à porta virtual no servidor VPN. Para que os utilizadores tenham acesso, devem fornecer o seu nome de utilizador e combinação de palavra-passe e outras informações que controlam. Esta informação deve ser fidedigna e não facilmente duplicada. Pode incluir um número de telemóvel, um número fixo ou uma aplicação num dispositivo móvel.

Antes da disponibilidade da extensão NPS para o Azure, os clientes que quisessem implementar uma verificação em duas etapas para ambientes integrados de NPS e MFA tinham de configurar e manter um servidor MFA separado num ambiente no local. Este tipo de autenticação é oferecido pelo Remote Desktop Gateway e pelo Azure Multi-Factor Authentication Server utilizando o RADIUS.

Com a extensão NPS para o Azure, as organizações podem garantir a autenticação do cliente RADIUS, implantando uma solução MFA baseada no local ou uma solução MFA baseada na nuvem.

## <a name="authentication-flow"></a>Fluxo de autenticação

Quando os utilizadores se ligam a uma porta virtual num servidor VPN, devem primeiro autenticar utilizando uma variedade de protocolos. Os protocolos permitem a utilização de uma combinação de modelos de nome de utilizador e palavra-passe e métodos de autenticação baseados em certificados.

Além de autenticar e verificar a sua identidade, os utilizadores devem ter as permissões de acesso ao acesso ao acesso. Em implementações simples, as permissões de acesso de marcação que permitem o acesso são definidas diretamente nos objetos do utilizador do Ative Directory.

![Separador de marcação em Utilizadores de Diretório Ativo e propriedades de utilizadores de computadores](./media/howto-mfa-nps-extension-vpn/image1.png)

Em implementações simples, cada servidor VPN concede ou nega o acesso com base em políticas definidas em cada servidor VPN local.

Em implementações maiores e mais escaláveis, as políticas que concedem ou negam o acesso VPN são centralizadas nos servidores RADIUS. Nestes casos, o servidor VPN funciona como um servidor de acesso (cliente RADIUS) que encaminha pedidos de ligação e mensagens de conta para um servidor RADIUS. Para se ligarem à porta virtual no servidor VPN, os utilizadores devem ser autenticados e satisfazer as condições definidas centralmente nos servidores RADIUS.

Quando a extensão NPS para Azure é integrada com o NPS, um fluxo de autenticação bem-sucedido resulta, da seguinte forma:

1. O servidor VPN recebe um pedido de autenticação de um utilizador VPN que inclui o nome de utilizador e a palavra-passe para se ligar a um recurso, como uma sessão de Desktop Remoto.
2. Atuando como cliente RADIUS, o servidor VPN converte o pedido numa mensagem RADIUS *Access-Request e envia-o* (com uma palavra-passe encriptada) para o servidor RADIUS onde está instalada a extensão NPS.
3. O nome de utilizador e a combinação de palavra-passe são verificados no Ative Directory. Se o nome de utilizador ou a palavra-passe estiverem incorretos, o RADIUS Server envia uma mensagem *Access-Reject.*
4. Se todas as condições, conforme especificado nas Políticas de Pedido de Conexão e De Rede NPS, forem satisfeitas (por exemplo, restrições de data do dia ou de grupo), a extensão NPS desencadeia um pedido de autenticação secundária com autenticação multi-factor Azure.
5. A Azure Multi-Factor Authentication comunica com o Azure Ative Directory, recupera os dados do utilizador e executa a autenticação secundária utilizando o método configurado pelo utilizador (chamada de telemóvel, mensagem de texto ou aplicação móvel).
6. Quando o desafio MFA é bem sucedido, a Autenticação Multi-Factor Azure comunica o resultado à extensão NPS.
7. Após a tentativa de ligação ser autenticada e autorizada, o NPS onde a extensão é instalada envia uma mensagem RADIUS *Access-Accept* para o servidor VPN (cliente RADIUS).
8. O utilizador tem acesso à porta virtual no servidor VPN e estabelece um túnel VPN encriptado.

## <a name="prerequisites"></a>Pré-requisitos

Esta secção detalha os pré-requisitos que devem ser preenchidos antes de poder integrar o MFA com a VPN. Antes de começar, deve ter os seguintes pré-requisitos no lugar:

* Infraestrutura VPN
* Função de Política de Rede e Serviços de Acesso
* Licença de autenticação multi-factor Azure
* Software windows Server
* Bibliotecas
* Azure Ative Directory (Azure AD) sincronizado com o Ative Directory no local
* Azure Ative Directory GUID ID

### <a name="vpn-infrastructure"></a>Infraestrutura VPN

Este artigo pressupõe que tem uma infraestrutura VPN em funcionamento que utiliza o Microsoft Windows Server 2016 e que o seu servidor VPN não está atualmente configurado para encaminhar pedidos de ligação para um servidor RADIUS. No artigo, configura-se a infraestrutura VPN para utilizar um servidor RADIUS central.

Se não tiver uma infraestrutura VPN em funcionamento, pode rapidamente criar uma seguindo as orientações em inúmeros tutoriais de configuração VPN que pode encontrar na Microsoft e em sites de terceiros.

### <a name="the-network-policy-and-access-services-role"></a>A função de Política de Rede e Serviços de Acesso

A Política de Rede e os Serviços de Acesso fornecem o servidor RADIUS e a funcionalidade do cliente. Este artigo pressupõe que instalou a função de Política de Rede e Serviços de Acesso num servidor de membros ou controlador de domínio no seu ambiente. Neste guia, configura RADIUS para uma configuração VPN. Instale a função De Política de Rede e Serviços de Acesso num servidor *diferente do* seu servidor VPN.

Para obter informações sobre a instalação do serviço de funções de política de rede e serviços de acesso, o Windows Server 2012 ou mais tarde, consulte [instalar um Servidor de Política de Saúde NAP](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)). O NAP é prectado no Windows Server 2016. Para uma descrição das melhores práticas para os NPS, incluindo a recomendação de instalar NPS num controlador de domínio, consulte [as melhores práticas para NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)).

### <a name="azure-mfa-license"></a>Licença Azure MFA

É necessária uma licença para autenticação multi-factor Azure, e está disponível através de um Azure AD Premium, Mobilidade Empresarial + Segurança ou uma licença autónoma de Autenticação Multi-Factor. As licenças baseadas no consumo para MFA Azure, como por utilizador ou por licença de autenticação, não são compatíveis com a extensão NPS. Para mais informações, consulte [Como obter a autenticação multi-factor Azure](concept-mfa-licensing.md). Para efeitos de teste, pode utilizar uma subscrição experimental.

### <a name="windows-server-software"></a>Software windows Server

A extensão NPS requer o Windows Server 2008 R2 SP1 ou mais tarde, com a função de Política de Rede e Serviços de Acesso instalada. Todos os passos deste guia foram realizados com o Windows Server 2016.

### <a name="libraries"></a>Bibliotecas

As seguintes bibliotecas são instaladas automaticamente com a extensão NPS:

-    [Pacotes redistribuíveis Visual C++ para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Módulo de Diretório Ativo Microsoft Azure para windows PowerShell versão 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Se o Módulo PowerShell do Diretor Ativo microsoft Azure ainda não estiver presente, este é instalado com um script de configuração que executou como parte do processo de configuração. Não é necessário instalar o módulo com antecedência se ainda não estiver instalado.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Diretório Ativo Azure sincronizado com diretório ativo no local

Para utilizar a extensão NPS, os utilizadores no local devem ser sincronizados com o Azure Ative Directory e habilitados para MFA. Este guia pressupõe que os utilizadores no local estão sincronizados com o Azure Ative Directory via Azure AD Connect. As instruções para permitir os utilizadores de MFA são fornecidas abaixo.

Para obter informações sobre o Azure AD Connect, consulte [Integrar os seus diretórios no local com o Azure Ative Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Ative Directory GUID ID

Para instalar a extensão NPS, é necessário conhecer o GUID do Diretório Ativo Azure. As instruções para encontrar o GUID do Diretório Ativo Azure são fornecidas na secção seguinte.

## <a name="configure-radius-for-vpn-connections"></a>Configure RADIUS para ligações VPN

Se instalou a função NPS num servidor de membros, tem de o configurar para autenticar e autorizar o cliente VPN que solicita ligações VPN. 

Esta secção pressupõe que instalou a função de Política de Rede e Serviços de Acesso, mas não a configuraram para utilização na sua infraestrutura.

> [!NOTE]
> Se já tiver um servidor VPN em funcionamento que utilize um servidor RADIUS centralizado para autenticação, pode saltar esta secção.
>

### <a name="register-server-in-active-directory"></a>Registre o servidor no Diretório Ativo

Para funcionar corretamente neste cenário, o servidor NPS deve ser registado no Ative Directory.

1. Abra o Gestor de Servidores.

2. No Gestor do Servidor, selecione **Ferramentas**e, em seguida, selecione **O Servidor de Política de Rede**.

3. Na consola Network Policy Server, clique com o botão direito **NPS (Local)** e, em seguida, **selecione o servidor Registar-se no Ative Directory**. Selecione **OK** duas vezes.

    ![Registar servidor na opção de menu ative directy](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Deixe a consola aberta para o próximo procedimento.

### <a name="use-wizard-to-configure-the-radius-server"></a>Utilize o assistente para configurar o servidor RADIUS

Pode utilizar uma opção de configuração padrão (baseada em assistentes) ou avançada para configurar o servidor RADIUS. Esta secção pressupõe que está a utilizar a opção de configuração padrão baseada no assistente.

1. Na consola Network Policy Server, selecione **NPS (Local)**.

2. Em **Configuração Padrão**, selecione **RADIUS Server para Ligações de Marcação ou VPN**e, em seguida, selecione **Configure VPN ou Dial-Up**.

    ![Configure o servidor RADIUS para ligações de marcação ou VPN](./media/howto-mfa-nps-extension-vpn/image3.png)

3. Na janela **Select Dial-up ou Virtual Private Network Type,** selecione **Virtual Private Network Connections**e, em seguida, selecione **Next**.

    ![Configurar ligações de rede privada virtual](./media/howto-mfa-nps-extension-vpn/image4.png)

4. Na janela **'Dial-Up' ou VPN,** selecione **Add**.

5. Na janela do **cliente New RADIUS,** forneça um nome amigável, insira o nome ou endereço IP resolúvel do servidor VPN e, em seguida, introduza uma senha secreta partilhada. Faça a senha secreta partilhada longa e complexa. Grava, porque vais precisar dele na próxima secção.

    ![Criar uma janela de cliente RADIUS nova](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Selecione **OK**e, em seguida, selecione **Seguinte**.

7. Na janela **Configure Authentication Methods,** aceite a seleção predefinida **(Microsoft Encrypted Authentication version 2 [MS-CHAPv2])** ou escolha outra opção e selecione **Seguinte**.

    > [!NOTE]
    > Se configurar o Protocolo de Autenticação Extensível (EAP), deve utilizar o Microsoft Challenge-Handshake Protocolo de Autenticação (CHAPv2) ou o Protocolo de Autenticação Extensível Protegido (PEAP). Nenhum outro EAP é apoiado.

8. Na janela **"Specify User Groups",** selecione **Adicionar**e, em seguida, selecione um grupo apropriado. Se não existir nenhum grupo, deixe a seleção em branco para conceder acesso a todos os utilizadores.

    ![Especifique a janela do Grupo de Utilizadores para permitir ou negar o acesso](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Selecione **Seguinte**.

10. Na janela **'Filtros IP's Specify,** selecione **Seguinte**.

11. Na janela **Definições de encriptação especifique,** aceite as definições predefinidos e, em seguida, selecione **Seguinte**.

    ![A janela Definições de encriptação especifique](./media/howto-mfa-nps-extension-vpn/image8.png)

12. Na janela **'Especificar um Nome real',** deixar o nome do reino em branco, aceitar a definição predefinitiva e, em seguida, selecionar **Seguinte**.

    ![A janela Especificar um Nome real](./media/howto-mfa-nps-extension-vpn/image9.png)

13. Na janela de novos acessos de acesso a novos acessos de acesso ou rede privada virtual e na janela **de clientes RADIUS,** selecione **Acabamento**.

    ![Janela de configuração concluída](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Verifique a configuração RADIUS

Esta secção detalha a configuração criada através do assistente.

1. No Servidor de Política de Rede, na consola NPS (local), expanda **os Clientes RADIUS**e, em seguida, selecione **clientes RADIUS**.

2. No painel de detalhes, clique com o botão direito no cliente RADIUS que criou e, em seguida, selecione **Propriedades**. As propriedades do seu cliente RADIUS (o servidor VPN) devem ser como as mostradas aqui:

    ![Verifique as propriedades e configuração VPN](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Selecione **Cancelar**.

4. No Servidor de Política de Rede, na consola NPS (local), expanda **as políticas**e, em seguida, selecione Políticas de Pedido **de Ligação**. A política de Ligações VPN é apresentada como mostrado na seguinte imagem:

    ![Política de pedido de ligação que mostra a política de ligação VPN](./media/howto-mfa-nps-extension-vpn/image12.png)

5. No âmbito **de Políticas**, selecione Políticas **de Rede**. Deverá ver uma política de conexões virtual private network (VPN) que se assemelha à política mostrada na seguinte imagem:

    ![Políticas de rede que mostram a política de conexões de rede privada virtual](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Configure o seu servidor VPN para utilizar a autenticação RADIUS

Nesta secção, configura o seu servidor VPN para utilizar a autenticação RADIUS. As instruções pressupõem que tem uma configuração de funcionamento de um servidor VPN, mas não o configuraram para utilizar a autenticação RADIUS. Depois de configurar o servidor VPN, confirme que a sua configuração está a funcionar como esperado.

> [!NOTE]
> Se já tiver uma configuração de servidor VPN em funcionamento que utilize a autenticação RADIUS, pode saltar esta secção.
>

### <a name="configure-authentication-provider"></a>Configure fornecedor de autenticação

1. No servidor VPN, abra o Gestor do Servidor.

2. No Gestor do Servidor, selecione **Ferramentas**e, em seguida, selecione **Encaminhamento e Acesso Remoto**.

3. Na janela **de encaminhamento e acesso remoto,** clique à direita ** \<server name> (local)** e, em seguida, selecione **Propriedades**.

4. Na janela ** \<server name> propriedades (locais),** selecione o **separador Segurança.**

5. No separador **Segurança,** no **fornecedor de autenticação,** selecione **RADIUS Authentication**e, em seguida, selecione **Configure**.

    ![Configure o fornecedor de autenticação RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)

6. Na janela **de autenticação RADIUS,** selecione **Adicionar**.

7. Na janela **do Servidor Add RADIUS,** faça o seguinte:

    a. Na caixa de **nomes do Servidor,** introduza o nome ou endereço IP do servidor RADIUS que configura na secção anterior.

    b. Para o **segredo partilhado**, selecione **Change**, e, em seguida, insira a senha secreta partilhada que criou e gravou anteriormente.

    c. Na caixa **de tempo (segundos),** insira um valor de **30**.  
    O valor do tempo limite é necessário para permitir tempo suficiente para completar o segundo fator de autenticação. Algumas VPNs ou regiões requerem configurações de tempo superior a 30 segundos para evitar que os utilizadores recebam várias chamadas telefónicas. Se os utilizadores experimentarem este problema, aumente o valor **de tempo de descodução (segundos)** em incrementos de 30 segundos até que o problema não se repita.

    ![Adicione a janela do servidor RADIUS configurando o tempo de desaporamento](./media/howto-mfa-nps-extension-vpn/image16.png) 

8. Selecione **OK**.

### <a name="test-vpn-connectivity"></a>Conectividade VPN de teste

Nesta secção, confirma que o cliente VPN é autenticado e autorizado pelo servidor RADIUS quando tenta ligar-se à porta virtual VPN. As instruções pressupõem que está a utilizar o Windows 10 como cliente VPN.

> [!NOTE]
> Se já configurar um cliente VPN para ligar ao servidor VPN e tiver guardado as definições, pode saltar os passos relacionados com a configuração e a poupança de um objeto de ligação VPN.
>

1. No seu computador cliente VPN, selecione o botão **Iniciar** e, em seguida, selecione o botão **Definições.**

2. Na janela Definições do **Windows,** selecione **Rede & Internet**.

3. Selecione **VPN**.

4. **Selecione Adicione uma ligação VPN**.

5. Na janela **de ligação Add a VPN,** na caixa de **provedor VPN,** selecione **Windows (incorporado)**, complete os restantes campos, conforme apropriado, e, em seguida, selecione **Guardar**.

    ![A janela "Adicionar uma ligação VPN"](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Vá ao **Painel de Controlo**e, em seguida, selecione Network and Sharing **Center**.

7. Selecione **Configurações do adaptador de alteração**.

    ![Centro de Rede e Partilha - Alterar configurações do adaptador](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Clique com o botão direito na ligação de rede VPN e, em seguida, selecione **Propriedades**.

9. Na janela de propriedades VPN, selecione o **separador Segurança.**

10. No separador **Segurança,** certifique-se de que apenas a **Versão 2 do Microsoft CHAP (MS-CHAP v2)** está selecionada e, em seguida, selecione **OK**.

    ![A opção "Permitir estes protocolos"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Clique com o botão direito na ligação VPN e, em seguida, selecione **Connect**.

12. Na janela **Definições,** selecione **Connect**.  
    Uma ligação bem sucedida aparece no registo de Segurança, no servidor RADIUS, como O ID do Evento 6272, como mostrado aqui:

    ![Janela propriedades do evento mostrando uma ligação bem sucedida](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Raio de resolução de problemas

Assuma que a sua configuração VPN estava a funcionar antes de configurar o servidor VPN para utilizar um servidor RADIUS centralizado para autenticação e autorização. Se a configuração estava a funcionar, é provável que o problema seja causado por uma configuração errada do servidor RADIUS ou pela utilização de um nome de utilizador ou palavra-passe inválido. Por exemplo, se utilizar o sufixo UPN alternativo no nome de utilizador, a tentativa de inscrição pode falhar. Use o mesmo nome de conta para obter os melhores resultados.

Para resolver estes problemas, um lugar ideal para começar é examinar os registos de eventos de Segurança no servidor RADIUS. Para economizar tempo à procura de eventos, pode utilizar a versão personalizada da Política de Rede e do Servidor de Acesso baseada em funções no Visualizador de Eventos, como mostrado aqui. "Event ID 6273" indica eventos em que o NPS negou o acesso a um utilizador.

![Espectador de eventos mostrando eventos NPAS](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Configurar a autenticação multi-factor

Para obter assistência configurar utilizadores para autenticação multi-factor consulte os artigos [Planejando uma implementação de autenticação multi-factor Azure baseada na nuvem](howto-mfa-getstarted.md#create-conditional-access-policy) e [Crie a minha conta para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Instale e configuure a extensão NPS

Esta secção fornece instruções para configurar a VPN para utilizar o MFA para autenticação do cliente com o servidor VPN.

> [!NOTE]
> A chave do registo REQUIRE_USER_MATCH é sensível a casos. Todos os valores devem ser definidos no formato UPPER CASE.
>

Depois de instalar e configurar a extensão NPS, toda a autenticação do cliente baseada em RADIUS que é processada por este servidor é necessária para utilizar o MFA. Se todos os seus utilizadores VPN não estiverem inscritos na Autenticação Multi-Factor Azure, pode fazer qualquer um dos seguintes:

* Crie outro servidor RADIUS para autenticar utilizadores que não estejam configurados para utilizar O MFA.

* Crie uma entrada de registo que permita aos utilizadores desafiados fornecer um segundo fator de autenticação se estiverem inscritos na Autenticação Multi-Factor Azure.

Crie um novo valor de cadeia nomeado _REQUIRE_USER_MATCH em HKLM\SOFTWARE\Microsoft\AzureMfa_, e deduça o valor para *VERDADEIRO* ou *FALSO*.

![A definição "Requer o Jogo do Utilizador"](./media/howto-mfa-nps-extension-vpn/image34.png)

Se o valor for definido para *TRUE* ou estiver em branco, todos os pedidos de autenticação estão sujeitos a um desafio MFA. Se o valor for definido como *FALSE*, os desafios do MFA são emitidos apenas para utilizadores que estejam inscritos na Autenticação Multi-Factor Azure. Utilize a definição *FALSE* apenas em ambientes de teste ou de produção durante um período de embarque.



### <a name="obtain-the-azure-active-directory-tenant-id"></a>Obtenha o ID do inquilino do Azure Ative Directory

Como parte da configuração da extensão NPS, você deve fornecer credenciais de administrador e o ID do seu inquilino AD Azure. Para obter a iD do inquilino, complete os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global do inquilino Azure.
1. No menu do portal Azure, selecione **Azure Ative Directory,** ou procure e selecione **O Diretório Ativo Azure** a partir de qualquer página.
1. Na página **geral,** é mostrada a informação do *Arrendatário.* Ao lado do ID do *inquilino,* selecione o ícone **Copy,** como mostra o seguinte exemplo de imagem:

   ![Obter a ID do Inquilino do portal Azure](./media/howto-mfa-nps-extension-vpn/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Instale a extensão NPS

A extensão NPS deve ser instalada num servidor que tenha a função de Política de Rede e Serviços de Acesso instalada e que funciona como o servidor RADIUS no seu design. *Não* instale a extensão NPS no seu servidor VPN.

1. Descarregue a extensão NPS do [Microsoft Download Center](https://aka.ms/npsmfa).

2. Copie o ficheiro executável de configuração* (NpsExtnForAzureMfaInstaller.exe) *para o servidor NPS.

3. No servidor NPS, clique duas vezes **NpsExtnForAzureMfaInstaller.exe** e, se for solicitado, selecione **Executar**.

4. Na **janela de configuração NPS Para Azure MFA,** reveja os termos da licença de software, selecione o I concorda com a caixa **de verificação de termos e condições da licença** e, em seguida, selecione **Instalar**.

    ![A janela "Extensão NPS para configuração Azure MFA"](./media/howto-mfa-nps-extension-vpn/image36.png)

5. Na **extensão NPS para a janela de configuração Azure MFA,** selecione **Fechar**.  

    ![A janela de confirmação "Setup Successful"](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Configure certificados para utilização com a extensão NPS utilizando um script PowerShell

Para garantir comunicações e garantias seguras, configure certificados para utilização pela extensão NPS. Os componentes NPS incluem um script Windows PowerShell que configura um certificado auto-assinado para uso com NPS.

O script executa as seguintes ações:

* Cria um certificado auto-assinado.
* Associa a chave pública do certificado ao principal de serviço na Azure AD.
* Guarda o certificado na loja de máquinas local.
* Concede ao utilizador da rede acesso à chave privada do certificado.
* Reinicia o serviço NPS.

Se quiser utilizar os seus próprios certificados, deve associar a chave pública do seu certificado ao principal do serviço na Azure AD, e assim por diante.

Para utilizar o script, forneça a extensão com as suas credenciais administrativas do Azure Ative Directory e o ID do inquilino do Azure Ative Que copiou anteriormente. A conta deve estar no mesmo inquilino da Ad AZure que deseja permitir a extensão. Execute o script em cada servidor NPS onde instala a extensão NPS.

1. Executar o Windows PowerShell como administrador.

2. Na ferência de comando PowerShell, introduza **o cd "c:\Program Files\Microsoft\AzureMfa\Config"** e, em seguida, selecione Enter.

3. Na próxima indicação de comando, introduza **.\AzureMfaNpsExtnConfigSetup.ps1**e, em seguida, selecione Enter. O script verifica se o módulo Azure AD PowerShell está instalado. Se não for instalado, o script instala o módulo para si.

    ![Executando o script de configuração AzureMfsNpsExtnConfigSetup.ps1](./media/howto-mfa-nps-extension-vpn/image38.png)

    Se tiver um erro de segurança devido ao TLS, ative o TLS 1.2 utilizando o `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` comando a partir da sua mensagem PowerShell.
    
    Após o script verificar a instalação do módulo PowerShell, apresenta a janela de inscrição do módulo powerShell do Azure Ative Directory.

4. Introduza as suas credenciais e palavra-passe do administrador AZure e, em seguida, selecione **Iniciar sôm.**

    ![Autenticar para Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. Na hora do comando, cole a identificação do inquilino que copiou anteriormente e, em seguida, selecione Enter.

    ![Insira o ID do inquilino Azure AD copiado antes](./media/howto-mfa-nps-extension-vpn/image40.png)

    O script cria um certificado auto-assinado e executa outras alterações de configuração. A saída é assim na seguinte imagem:

    ![Janela PowerShell mostrando certificado auto-assinado](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Reinicie o servidor.

### <a name="verify-the-configuration"></a>Verificar a configuração

Para verificar a configuração, tem de estabelecer uma nova ligação VPN com o servidor VPN. Depois de ter introduzido com sucesso as suas credenciais para autenticação primária, a ligação VPN aguarda que a autenticação secundária tenha sucesso antes de a ligação ser estabelecida, como mostrado abaixo.

![A janela VPN de Definições do Windows](./media/howto-mfa-nps-extension-vpn/image42.png)

Se autenticar com sucesso o método de verificação secundária que configura anteriormente em Azure MFA, está ligado ao recurso. No entanto, se a autenticação secundária não for bem sucedida, é-lhe negado o acesso ao recurso.

No exemplo seguinte, a aplicação Microsoft Authenticator num Windows Phone fornece a autenticação secundária:

![Exemplo de pedido de MFA no Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

Depois de autenticar com sucesso utilizando o método secundário, é-lhe concedido acesso à porta virtual no servidor VPN. Como foi obrigado a utilizar um método de autenticação secundária utilizando uma aplicação móvel num dispositivo de confiança, o processo de inscrição é mais seguro do que se estivesse a usar apenas um nome de utilizador e uma combinação de palavra-passe.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Ver registos do Espectador de Eventos para eventos de login com sucesso

Para visualizar eventos de login bem-sucedidos nos registos do Visualizador de Eventos do Windows, consulte o registo de Segurança do Windows, no servidor NPS, introduzindo o seguinte comando PowerShell:

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Visualizador de eventos de segurança PowerShell](./media/howto-mfa-nps-extension-vpn/image44.png)

Também pode ver o registo de segurança ou a visualização personalizada da Política de Rede e serviços de acesso, como mostrado aqui:

![Registo do servidor de política de rede de exemplo](./media/howto-mfa-nps-extension-vpn/image45.png)

No servidor onde instalou a extensão NPS para autenticação multi-factor Azure, pode encontrar registos de aplicações do Event Viewer específicos da extensão nos *Registos de Aplicações e Serviços\Microsoft\AzureMfa*.

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Exemplo De Espectador de EventoS AuthZ logs painel](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Se a configuração não estiver a funcionar como esperado, comece a resolver problemas verificando se o utilizador está configurado para utilizar MFA. Ter o utilizador ligado ao [portal Azure](https://portal.azure.com). Se o utilizador for solicitado para a autenticação secundária e puder autenticar com sucesso, pode eliminar uma configuração incorreta de MFA como um problema.

Se a MFA estiver a trabalhar para o utilizador, reveja os registos relevantes do Observador de Eventos. Os registos incluem o evento de segurança, gateway operacional, e registos de autenticação multi-factor Azure que são discutidos na secção anterior.

Um exemplo de um registo de segurança que exibe um evento de login falhado (iD 6273 do evento) é mostrado aqui:

![Registo de segurança mostrando um evento de login falhado](./media/howto-mfa-nps-extension-vpn/image47.png)

Um evento relacionado a partir do registo de autenticação multi-factor Azure é mostrado aqui:

![Registos de autenticação multi-factor Azure](./media/howto-mfa-nps-extension-vpn/image48.png)

Para fazer uma resolução avançada de problemas, consulte os ficheiros de registo de formato de base de dados NPS onde o serviço NPS está instalado. Os ficheiros de registo são criados na pasta _%SystemRoot%\System32\Logs_ como ficheiros de texto delimitados por vírgula. Para obter uma descrição dos ficheiros de registo, consulte [os ficheiros de registo do formato de base de dados do NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)).

As entradas nestes ficheiros de registo são difíceis de interpretar, a menos que as exporte para uma folha de cálculo ou uma base de dados. Pode encontrar muitas ferramentas de análise do Serviço de Autenticação da Internet (IAS) online para o ajudar a interpretar os ficheiros de registo. A saída de uma dessas [aplicações de shareware](https://www.deepsoftware.com/iasviewer) transferíveis é mostrada aqui:

![Amostra Shareware app IAS parser](./media/howto-mfa-nps-extension-vpn/image49.png)

Para fazer uma resolução adicional de problemas, pode utilizar um analisador de protocolos como o Wireshark ou [o Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide). A imagem a seguir da Wireshark mostra as mensagens RADIUS entre o servidor VPN e o NPS.

![Microsoft Message Analyzer mostrando tráfego filtrado](./media/howto-mfa-nps-extension-vpn/image50.png)

Para obter mais informações, consulte [Integre a sua infraestrutura de NPS existente com autenticação multi-factor Azure.](howto-mfa-nps-extension.md)

## <a name="next-steps"></a>Passos seguintes

[Obtenha autenticação multi-factor Azure](concept-mfa-licensing.md)

[Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS](howto-mfaserver-nps-rdg.md)

[Integrar os diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md)