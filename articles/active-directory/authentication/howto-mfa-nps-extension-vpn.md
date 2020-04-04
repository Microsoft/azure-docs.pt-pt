---
title: VPN com Azure MFA usando a extensão NPS - Diretório Ativo Azure
description: Integre a sua infraestrutura VPN com o Azure MFA utilizando a extensão do Servidor de Política de Rede para o Microsoft Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec8d5b66c71c558e56f3d1f48cec96d7cc487552
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654117"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integre a sua infraestrutura VPN com o Azure MFA utilizando a extensão do Servidor de Política de Rede para o Azure

A extensão do Servidor de Política de Rede (NPS) para o Azure permite que as organizações protejam a autenticação do cliente do Serviço de Utilizador de Autenticação Remota (RADIUS) utilizando a [autenticação multi-factor Azure (MFA)](howto-mfaserver-nps-rdg.md)baseada na nuvem, que fornece uma verificação em duas etapas.

Este artigo fornece instruções para integrar a infraestrutura de NPS com MFA utilizando a extensão NPS para O Azure. Este processo permite uma verificação segura em duas etapas para os utilizadores que tentam ligar-se à sua rede utilizando uma VPN.

A Política de Rede e os Serviços de Acesso dão às organizações a capacidade de:

* Atribuir uma localização central para a gestão e controlo dos pedidos de rede para especificar:

  * Quem pode ligar

  * Que horas de ligação diária são permitidas

  * A duração das ligações

  * O nível de segurança que os clientes devem usar para ligar

    Em vez de especificar as políticas em cada servidor VPN ou Remote Desktop Gateway, faça-o depois de se encontrarem num local central. O protocolo RADIUS é utilizado para fornecer autenticação centralizada, autorização e contabilidade (AAA).

* Estabeleça e aplique políticas de saúde dos clientes de Proteção de Acesso à Rede (NAP) que determinam se os dispositivos têm acesso ilimitado ou restrito aos recursos da rede.

* Forneça uma forma de impor a autenticação e autorização de acesso a 802.1x pontos de acesso sem fios capazes de acesso e comutadores Ethernet.
  Para mais informações, consulte o Servidor de [Política de Rede](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top).

Para aumentar a segurança e fornecer um alto nível de conformidade, as organizações podem integrar o NPS com a Autenticação Multi-Factor Azure para garantir que os utilizadores usam verificação em duas etapas para se conectarem à porta virtual no servidor VPN. Para que os utilizadores tenham acesso, devem fornecer o seu nome de utilizador e combinação de palavras-passe e outras informações que controlam. Estas informações devem ser confiáveis e não facilmente duplicadas. Pode incluir um número de telemóvel, um número fixo ou uma aplicação num dispositivo móvel.

Antes da disponibilidade da extensão NPS para o Azure, os clientes que quisessem implementar uma verificação em duas etapas para ambientes Integrados de NPS e MFA tiveram de configurar e manter um servidor MFA separado num ambiente no local. Este tipo de autenticação é oferecido pelo Remote Desktop Gateway e pelo Azure Multi-Factor Authentication Server utilizando o RADIUS.

Com a extensão nps para o Azure, as organizações podem garantir a autenticação do cliente RADIUS implementando uma solução MFA baseada no local ou uma solução MFA baseada na nuvem.

## <a name="authentication-flow"></a>Fluxo de autenticação

Quando os utilizadores se ligam a uma porta virtual num servidor VPN, devem primeiro autenticar utilizando uma variedade de protocolos. Os protocolos permitem a utilização de uma combinação de nome de utilizador e password e métodos de autenticação baseados em certificados.

Além de autenticar e verificar a sua identidade, os utilizadores devem ter as permissões de acesso telefónico adequadas. Em implementações simples, as permissões de acesso que permitem o acesso são definidas diretamente nos objetos de utilizador do Diretório Ativo.

![Separador de acesso telefónico nas propriedades dos utilizadores de diretórios ativos e dos computadores](./media/howto-mfa-nps-extension-vpn/image1.png)

Em implementações simples, cada servidor VPN concede ou nega o acesso com base em políticas definidas em cada servidor VPN local.

Em implementações maiores e mais escaláveis, as políticas que concedem ou negam o acesso à VPN são centralizadas nos servidores RADIUS. Nestes casos, o servidor VPN funciona como um servidor de acesso (cliente RADIUS) que reencaminha pedidos de ligação e mensagens de conta para um servidor RADIUS. Para se ligarà porta virtual do servidor VPN, os utilizadores devem ser autenticados e satisfazer as condições que são definidas centralmente nos servidores RADIUS.

Quando a extensão NPS para Azure é integrada com o NPS, um fluxo de autenticação bem sucedido resulta, da seguinte forma:

1. O servidor VPN recebe um pedido de autenticação de um utilizador VPN que inclui o nome de utilizador e a palavra-passe para a ligação a um recurso, como uma sessão de Ambiente de Trabalho Remoto.
2. Atuando como um cliente RADIUS, o servidor VPN converte o pedido para uma mensagem radius *Access-Request e envia-o* (com uma palavra-passe encriptada) para o servidor RADIUS onde a extensão NPS está instalada.
3. A combinação de nome de utilizador e palavra-passe é verificada no Diretório Ativo. Se o nome de utilizador ou a palavra-passe estiverem incorretos, o Servidor RADIUS envia uma mensagem de *rejeição de acesso.*
4. Se todas as condições, conforme especificado nas Políticas de Pedido de Ligação e Rede NPS, forem cumpridas (por exemplo, restrições de hora de dia ou de adesão em grupo), a extensão nps desencadeia um pedido de autenticação secundária com autenticação multi-factor Azure.
5. A Autenticação Multi-Factor Azure comunica com o Diretório Ativo Azure, recupera os detalhes do utilizador e executa a autenticação secundária utilizando o método configurado pelo utilizador (chamada de telemóvel, mensagem de texto ou aplicação móvel).
6. Quando o desafio MFA é bem sucedido, a Autenticação Multi-Factor Azure comunica o resultado com a extensão NPS.
7. Após a tentativa de ligação ser autenticada e autorizada, o NPS onde a extensão é instalada envia uma mensagem RADIUS *Access-Accept* para o servidor VPN (cliente RADIUS).
8. O utilizador tem acesso à porta virtual no servidor VPN e estabelece um túnel VPN encriptado.

## <a name="prerequisites"></a>Pré-requisitos

Esta secção detalha os pré-requisitos que devem ser concluídos antes de poder integrar o MFA com a VPN. Antes de começar, deve ter os seguintes pré-requisitos no lugar:

* Infraestrutura VPN
* Função de Política de Rede e Serviços de Acesso
* Licença de autenticação de vários fatores Azure
* Software Windows Server
* Bibliotecas
* Diretório Ativo Azure (Azure AD) sincronizado com diretório ativo no local
* Id guia do diretório ativo Azure

### <a name="vpn-infrastructure"></a>Infraestrutura VPN

Este artigo assume que tem uma infraestrutura VPN em funcionamento que utiliza o Microsoft Windows Server 2016 e que o seu servidor VPN não está atualmente configurado para encaminhar pedidos de ligação para um servidor RADIUS. No artigo, configura a infraestrutura VPN para utilizar um servidor CENTRAL RADIUS.

Se não tiver uma infraestrutura VPN em funcionamento, pode rapidamente criar uma seguindo a orientação em inúmeros tutoriais de configuração VPN que pode encontrar nos sites da Microsoft e de terceiros.

### <a name="the-network-policy-and-access-services-role"></a>O papel de Política de Rede e Serviços de Acesso

A Política de Rede e os Serviços de Acesso fornecem o servidor RADIUS e a funcionalidade do cliente. Este artigo pressupõe que instalou a função de Política de Rede e Serviços de Acesso num servidor ou controlador de domínio no seu ambiente. Neste guia, configura o RADIUS para uma configuração VPN. Instale a função Política de Rede e Serviços de Acesso num servidor *diferente do* seu servidor VPN.

Para obter informações sobre a instalação do serviço de função de Política de Rede e Serviços de Acesso Windows Server 2012 ou posteriormente, consulte instalar um Servidor de Política de [Saúde NAP](https://technet.microsoft.com/library/dd296890.aspx). O PAN está deprecida no Windows Server 2016. Para uma descrição das melhores práticas para nPS, incluindo a recomendação de instalar NPS num controlador de domínio, consulte [as melhores práticas para NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Licença Azure MFA

É necessária uma licença para autenticação multi-factor, e está disponível através de um Azure AD Premium, Enterprise Mobility + Security ou de uma licença autónoma de autenticação multi-factor. As licenças baseadas no consumo para o Azure MFA, tais como por utilizador ou por licença de autenticação, não são compatíveis com a extensão NPS. Para mais informações, consulte Como obter a [autenticação de multi-factors Azure](concept-mfa-licensing.md). Para efeitos de teste, pode utilizar uma subscrição experimental.

### <a name="windows-server-software"></a>Software Windows Server

A extensão NPS requer o Windows Server 2008 R2 SP1 ou mais tarde, com a função de Política de Rede e Serviços de Acesso instalada. Todos os passos deste guia foram realizados com o Windows Server 2016.

### <a name="libraries"></a>Bibliotecas

As seguintes bibliotecas são instaladas automaticamente com a extensão NPS:

-    [Pacotes Redistribuíveis Visual C++ para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Módulo de Diretório Ativo microsoft Azure para windows PowerShell versão 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Se o Módulo PowerShell do Microsoft Azure Ative Directory ainda não estiver presente, está instalado com um script de configuração que executa como parte do processo de configuração. Não há necessidade de instalar o módulo com antecedência se ainda não estiver instalado.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Diretório Ativo Azure sincronizado com diretório ativo no local

Para utilizar a extensão NPS, os utilizadores no local devem ser sincronizados com o Diretório Ativo Azure e habilitados para mFA. Este guia pressupõe que os utilizadores no local estão sincronizados com o Diretório Ativo Azure via Azure AD Connect. As instruções para permitir os utilizadores de MFA são fornecidas abaixo.

Para obter informações sobre o Azure AD Connect, consulte [Integrar os seus diretórios no local com o Diretório Ativo Azure](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Id guia do diretório ativo Azure

Para instalar a extensão NPS, precisa de conhecer o GUID do Diretório Ativo Azure. Na secção seguinte são fornecidas instruções para encontrar o GUIA do Diretório Ativo Azure.

## <a name="configure-radius-for-vpn-connections"></a>Configure RADIUS para ligações VPN

Se instalou a função NPS num servidor membro, tem de configurá-la para autenticar e autorizar o cliente VPN que solicita ligações VPN. 

Esta secção pressupõe que instalou a função de Política de Rede e Serviços de Acesso, mas não a configurou para utilização na sua infraestrutura.

> [!NOTE]
> Se já tiver um servidor VPN a funcionar que utilize um servidor RADIUS centralizado para autenticação, pode saltar esta secção.
>

### <a name="register-server-in-active-directory"></a>Registar servidor em Diretório Ativo

Para funcionar corretamente neste cenário, o servidor NPS deve ser registado no Diretório Ativo.

1. Abra o Gestor de Servidores.

2. No Gestor do Servidor, selecione **Ferramentas**, e, em seguida, selecione O Servidor de Política de **Rede**.

3. Na consola do Servidor de Política de Rede, clique no **nPS (Local)** e, em seguida, **selecione o servidor Register no Diretório Ativo**. Selecione **OK** duas vezes.

    ![Registe o servidor na opção de menu de Diretório Ativo](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Deixe a consola aberta para o próximo procedimento.

### <a name="use-wizard-to-configure-the-radius-server"></a>Utilize o assistente para configurar o servidor RADIUS

Pode utilizar uma opção de configuração padrão (baseada em assistentes) ou avançada para configurar o servidor RADIUS. Esta secção pressupõe que está a utilizar a opção de configuração padrão baseada no assistente.

1. Na consola do Servidor de Política de Rede, selecione **NPS (Local)**.

2. Sob **configuração padrão,** selecione **Radius Server para ligações de marcação para cima ou VPN**, e, em seguida, selecione **Configure VPN ou Dial-Up**.

    ![Configure o servidor RADIUS para ligações de acesso telefónico ou VPN](./media/howto-mfa-nps-extension-vpn/image3.png)

3. Na janela **Selecione Ligar para cima ou virtual ligações de rede privada,** selecione **Ligações de rede privadas virtuais,** e, em seguida, selecione **Next**.

    ![Configurar ligações de rede privada virtual](./media/howto-mfa-nps-extension-vpn/image4.png)

4. Na janela **'Ligar para cima' ou vpn Server,** selecione **Adicionar**.

5. Na janela do **cliente New RADIUS,** forneça um nome amigável, introduza o nome ou endereço IP do servidor VPN e, em seguida, introduza uma senha secreta partilhada. Faça a senha secreta partilhada longa e complexa. Grava, porque vais precisar dele na próxima secção.

    ![Criar uma janela de cliente new RADIUS](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Selecione **OK**, e, em seguida, selecione **Next**.

7. Na janela Métodos de **Autenticação Configurada,** aceite a seleção predefinida **(Versão de Autenticação Encriptada da Microsoft 2 [MS-CHAPv2])** ou escolha outra opção, e selecione **Next**.

    > [!NOTE]
    > Se configurar o Protocolo de Autenticação Extensível (EAP), deve utilizar o Protocolo de Autenticação de Apagamento de Desafios e Apertos de Mão (CHAPv2) ou o Protocolo de Autenticação Extensível Protegido (PEAP). Nenhum outro EAP é apoiado.

8. Na janela **'Especificar Grupos de Utilizadores',** selecione **Adicionar**, e, em seguida, selecione um grupo apropriado. Se não existir nenhum grupo, deixe a seleção em branco para conceder acesso a todos os utilizadores.

    ![Especificar janela dos Grupos de Utilizadores para permitir ou negar o acesso](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Selecione **Next**.

10. Na janela **'Especificar filtros IP',** selecione **Seguinte**.

11. Na janela **'Definições de encriptação' Especificar,** aceite as definições predefinidas e, em seguida, selecione **Next**.

    ![A janela de definições de encriptação especificar](./media/howto-mfa-nps-extension-vpn/image8.png)

12. Na janela **'Especificar um Nome realm',** deixe o nome do reino em branco, aceite a definição predefinida e, em seguida, selecione **Seguinte**.

    ![Especificar uma janela de nome do reino](./media/howto-mfa-nps-extension-vpn/image9.png)

13. Na janela de clientes De **Acesso Novo Telefónico ou Virtual Private Network e radius,** selecione **Finish**.

    ![Janela de configuração concluída](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Verifique a configuração RADIUS

Esta secção detalha a configuração que criou utilizando o assistente.

1. No Servidor de Política de Rede, na consola NPS (local), expanda **os Clientes RADIUS,** e depois selecione **Clientes RADIUS**.

2. No painel de detalhes, clique no cliente RADIUS que criou e, em seguida, selecione **Propriedades**. As propriedades do seu cliente RADIUS (o servidor VPN) devem ser como as mostradas aqui:

    ![Verifique as propriedades e configuração vpN](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Selecione **Cancelar**.

4. No Servidor de Política de Rede, na consola NPS (local), expanda **as Políticas**e, em seguida, selecione Políticas de Pedido de **Ligação**. A política vpn Connections é apresentada como mostrado na seguinte imagem:

    ![Política de pedido de ligação que mostra a política de ligação VPN](./media/howto-mfa-nps-extension-vpn/image12.png)

5. Em **Políticas,** selecione **Políticas de Rede.** Deve ver uma política de conexões da Rede Privada Virtual (VPN) que se assemelha à política mostrada na seguinte imagem:

    ![Políticas de rede que mostram a política de conexões de rede privada virtual](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Configure o seu servidor VPN para utilizar a autenticação RADIUS

Nesta secção, configura o seu servidor VPN para utilizar a autenticação RADIUS. As instruções pressupõem que tem uma configuração de funcionamento de um servidor VPN, mas não o configuraram para utilizar a autenticação RADIUS. Depois de configurar o servidor VPN, confirme que a sua configuração está a funcionar como esperado.

> [!NOTE]
> Se já tiver uma configuração de servidor VPN a funcionar que utiliza a autenticação RADIUS, pode saltar esta secção.
>

### <a name="configure-authentication-provider"></a>Configure fornecedor de autenticação

1. No servidor VPN, abra o Server Manager.

2. No Gestor do Servidor, selecione **Ferramentas**, e, em seguida, selecione **Encaminhamento e Acesso Remoto**.

3. Na janela **de Encaminhamento e Acesso Remoto,** o nome do servidor de clique direito ** \<> (local)** e, em seguida, selecione **Propriedades**.

4. No ** \<nome do servidor> janela propriedades (local),** selecione o separador **Segurança.**

5. No separador **Segurança,** sob **o fornecedor de autenticação,** selecione **autenticação RADIUS,** e, em seguida, selecione **Configurar**.

    ![Configure fornecedor de autenticação RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)

6. Na janela **radius authentication,** selecione **Adicionar**.

7. Na janela **Add RADIUS Server,** faça o seguinte:

    a. Na caixa de nome sinuosa, introduza o nome ou endereço IP do servidor RADIUS que configurava na secção anterior. **Server name**

    b. Para o **segredo partilhado**, selecione **Change**e, em seguida, introduza a senha secreta partilhada que criou e gravou anteriormente.

    c. Na caixa **time-out (segundos),** introduza um valor de **30**.  
    O valor do tempo é necessário para permitir tempo suficiente para completar o segundo fator de autenticação.

    ![Adicione a janela radius server configurando o time-out](./media/howto-mfa-nps-extension-vpn/image16.png)

8. Selecione **OK**.

### <a name="test-vpn-connectivity"></a>Conectividade VPN de teste

Nesta secção, confirma que o cliente VPN é autenticado e autorizado pelo servidor RADIUS quando tenta ligar-se à porta virtual VPN. As instruções pressupõem que está a utilizar o Windows 10 como cliente VPN.

> [!NOTE]
> Se já configurar um cliente VPN para ligar ao servidor VPN e tiver guardado as definições, pode ignorar os passos relacionados com a configuração e guardar um objeto de ligação VPN.
>

1. No computador cliente VPN, selecione o botão **Iniciar** e, em seguida, selecione o botão **Definições.**

2. Na janela **Definições do Windows,** selecione **Rede & Internet**.

3. Selecione **VPN**.

4. **Selecione Adicionar uma ligação VPN**.

5. Na **janela de ligação VpN Add,** na caixa de **fornecedor VPN,** selecione **Windows (incorporado)**, complete os restantes campos, conforme apropriado, e, em seguida, selecione **Guardar**.

    ![A janela "Adicionar uma ligação VPN"](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Vá ao **Painel**de Controlo e, em seguida, selecione **Network and Sharing Center**.

7. Selecione definições de **adaptador De alterar**.

    ![Network and Sharing Center - Alterar definições de adaptador](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Clique na ligação de rede VPN e, em seguida, selecione **Propriedades**.

9. Na janela de propriedades VPN, selecione o separador **Segurança.**

10. No separador **Segurança,** certifique-se de que apenas a **Microsoft CHAP Version 2 (MS-CHAP v2)** é selecionada e, em seguida, selecione **OK**.

    ![A opção "Permitir estes protocolos"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Clique na ligação VPN e, em seguida, selecione **Connect**.

12. Na janela **Definições,** selecione **Ligar**.  
    Uma ligação bem sucedida aparece no registo de Segurança, no servidor RADIUS, como Id do Evento 6272, como mostrado aqui:

    ![Janela de Propriedades de Eventos mostrando uma conexão bem sucedida](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>RAIO DE Resolução de Problemas

Assuma que a sua configuração VPN estava a funcionar antes de configurar o servidor VPN para utilizar um servidor RADIUS centralizado para autenticação e autorização. Se a configuração estava a funcionar, é provável que o problema seja causado por uma configuração errada do servidor RADIUS ou pela utilização de um nome de utilizador ou palavra-passe inválido. Por exemplo, se utilizar o sufixo alternativo UPN no nome de utilizador, a tentativa de iniciar o 'sign-in' poderá falhar. Use o mesmo nome de conta para obter os melhores resultados.

Para resolver estes problemas, o local ideal para começar é examinar os registos de eventos de segurança no servidor RADIUS. Para poupar tempo à procura de eventos, pode utilizar a visão personalizada da Política de Rede e do Servidor de Acesso baseado em papéis no Observador de Eventos, como mostrado aqui. O "Id do evento 6273" indica eventos em que o NPS negou o acesso a um utilizador.

![Espectador de eventos mostrando eventos nPAS](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Configurar a autenticação de vários fatores

Para assistência, configurar os utilizadores para autenticação multi-factor, consulte os artigos [planejando uma implementação de autenticação azure multi-factor baseada na nuvem](howto-mfa-getstarted.md#create-conditional-access-policy) e configurar a minha conta para [verificação em duas etapas](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Instalar e configurar a extensão NPS

Esta secção fornece instruções para configurar vpN para usar MFA para autenticação do cliente com o servidor VPN.

Depois de instalar e configurar a extensão NPS, toda a autenticação do cliente baseada em RADIUS que é processada por este servidor é necessária para utilizar o MFA. Se todos os seus utilizadores vpN não estiverem inscritos na Autenticação Multi-Factor Azure, pode fazer qualquer um dos seguintes utilizadores:

* Configure outro servidor RADIUS para autenticar utilizadores que não estejam configurados para utilizar o MFA.

* Crie uma entrada de registo que permita aos utilizadores desafiados fornecer um segundo fator de autenticação se estiverem matriculados na Autenticação Multi-Factor Azure.

Crie um novo valor de cadeia chamado _REQUIRE_USER_MATCH em HKLM\SOFTWARE\Microsoft\AzureMfa_, e defina o valor para *True* ou *False*.

![A definição "Requirer Jogo do Utilizador"](./media/howto-mfa-nps-extension-vpn/image34.png)

Se o valor for definido para *True* ou estiver em branco, todos os pedidos de autenticação estão sujeitos a um desafio mfa. Se o valor for definido para *falsos*desafios de MFA são emitidos apenas para utilizadores que estejam matriculados na Autenticação Multi-Factor Azure. Utilize a configuração *falsa* apenas em ambientes de ensaio ou em ambientes de produção durante um período de embarque.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Obtenha o ID GUIA de Diretório Ativo Azure

Como parte da configuração da extensão NPS, deve fornecer credenciais de administrador e a identificação do seu inquilino Azure AD. Obtenha o ID fazendo o seguinte:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global do inquilino Azure.

2. No menu do portal Azure, selecione **Azure Ative Directory,** ou procure e selecione **Azure Ative Directory** a partir de qualquer página.

3. Selecione **Propriedades**.

4. Para copiar o id da AD Azure, selecione o botão **Copiar.**

    ![ID do diretório Azure AD no portal Azure](./media/howto-mfa-nps-extension-vpn/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>Instale a extensão NPS

A extensão NPS deve ser instalada num servidor que tenha a função política de rede e serviços de acesso instalada e que funciona como servidor RADIUS no seu design. *Não* instale a extensão NPS no seu servidor VPN.

1. Descarregue a extensão NPS do [Microsoft Download Center](https://aka.ms/npsmfa).

2. Copie o ficheiro executável de configuração *(NpsExtnForAzureMfaInstaller.exe*) para o servidor NPS.

3. No servidor NPS, clique duas vezes **npsExtnForAzureMfaInstaller.exe** e, se for solicitado, selecione **Executar**.

4. Na **janela de configuração de extensão NPS Para Azure MFA,** reveja os termos da licença de software, selecione o **I concordo com os termos e condições** de verificação da licença e, em seguida, selecione **Instalar**.

    ![A janela "Extensão NPS para configuração de MFA" Azure](./media/howto-mfa-nps-extension-vpn/image36.png)

5. Na extensão NPS para a janela de **configuração Azure MFA,** selecione **Fechar**.  

    ![A janela de confirmação "Configuração bem sucedida"](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Configure certificados para utilização com a extensão NPS utilizando um script PowerShell

Para garantir comunicações seguras e garantias, configure os certificados para utilização pela extensão NPS. Os componentes NPS incluem um script Windows PowerShell que configura um certificado auto-assinado para uso com NPS.

O guião executa as seguintes ações:

* Cria um certificado auto-assinado.
* Associa a chave pública do certificado ao diretor de serviço em Azure AD.
* Armazena o certificado na loja de máquinas local.
* Concede ao utilizador da rede acesso à chave privada do certificado.
* Reinicia o serviço NPS.

Se quiser utilizar os seus próprios certificados, deve associar a chave pública do seu certificado ao diretor de serviço em Azure AD, e assim por diante.

Para utilizar o script, forneça a extensão com as suas credenciais administrativas de Diretório Ativo Azure e o ID de inquilino do Azure Ative Directory que copiou anteriormente. Executar o script em cada servidor NPS onde instala a extensão NPS.

1. Executar o Windows PowerShell como administrador.

2. No pedido de comando PowerShell, introduza **cd "c:\Program Files\Microsoft\AzureMfa\Config"** e, em seguida, selecione Enter.

3. No próximo pedido de comando, introduza **.\AzureMfaNpsExtnConfigSetup.ps1**, e, em seguida, selecione Enter. O script verifica se o módulo PowerShell Azure AD está instalado. Se não estiver instalado, o script instala o módulo para si.

    ![Executando o script de configuração AzureMfsNpsExtnConfigSetup.ps1](./media/howto-mfa-nps-extension-vpn/image38.png)

    Depois de o script verificar a instalação do módulo PowerShell, exibe a janela de entrada do módulo PowerShell do módulo PowerShell do Diretório Ativo Azure.

4. Introduza as credenciais e palavra-passe do administrador da AD Azure e, em seguida, selecione **Iniciar sessão**.

    ![Autenticar a Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. No pedido de comando, colhe a identificação do inquilino que copiou anteriormente e, em seguida, selecione Enter.

    ![Insere o Id do Diretório Azure AD copiado antes](./media/howto-mfa-nps-extension-vpn/image40.png)

    O script cria um certificado auto-assinado e executa outras alterações de configuração. A saída é assim na seguinte imagem:

    ![Janela PowerShell mostrando certificado auto-assinado](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Reiniciar o servidor.

### <a name="verify-the-configuration"></a>Verificar a configuração

Para verificar a configuração, deve estabelecer uma nova ligação VPN com o servidor VPN. Depois de ter introduzido com sucesso as suas credenciais para autenticação primária, a ligação VPN aguarda que a autenticação secundária tenha sucesso antes de a ligação ser estabelecida, como mostrado abaixo.

![A janela VPN de definições do Windows](./media/howto-mfa-nps-extension-vpn/image42.png)

Se autenticar com sucesso com o método de verificação secundária que configurado anteriormente no Azure MFA, está ligado ao recurso. No entanto, se a autenticação secundária não for bem sucedida, é-lhe negado o acesso ao recurso.

No exemplo seguinte, a aplicação Microsoft Authenticator num Windows Phone fornece a autenticação secundária:

![Exemplo De MFA solicitação no Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

Depois de autenticar com sucesso utilizando o método secundário, é-lhe concedido acesso à porta virtual no servidor VPN. Uma vez que foi obrigado a utilizar um método de autenticação secundária utilizando uma aplicação móvel num dispositivo de confiança, o processo de entrada é mais seguro do que se estivesse a utilizar apenas um nome de utilizador e uma combinação de palavra-passe.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Ver registos do Espectador de Eventos para eventos de login bem sucedidos

Para visualizar eventos de entrada bem-sucedidos no Windows Event Viewer consulta o registo de Segurança do Windows, no servidor NPS, ao introduzir o seguinte comando PowerShell:

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Espectador de eventos de segurança powerShell](./media/howto-mfa-nps-extension-vpn/image44.png)

Também pode ver o registo de segurança ou a visão personalizada da Política de Rede e serviços de acesso, como mostrado aqui:

![Registo do servidor de política de rede de exemplo](./media/howto-mfa-nps-extension-vpn/image45.png)

No servidor onde instalou a extensão NPS para autenticação de multi-factores, pode encontrar registos de aplicações do Observador de Eventos específicos da extensão em Registos de *Aplicação e Serviços\Microsoft\AzureMfa*.

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Painel de logs do espectador de eventos authZ](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Se a configuração não estiver a funcionar como esperado, comece a resolver problemas verificando se o utilizador está configurado para utilizar o MFA. Mande o utilizador ligar-se ao [portal Azure](https://portal.azure.com). Se o utilizador for solicitado para autenticação secundária e conseguir autenticar com sucesso, pode eliminar uma configuração incorreta do MFA como um problema.

Se o MFA estiver a trabalhar para o utilizador, reveja os registos do Espectador de Eventos relevantes. Os registos incluem o evento de segurança, os registos operacionais gateway e os registos de autenticação multi-factor Azure que são discutidos na secção anterior.

Um exemplo de um registo de segurança que exibe um evento de inscrição falhado (evento ID 6273) é mostrado aqui:

![Registo de segurança mostrando um evento de inscrição falhado](./media/howto-mfa-nps-extension-vpn/image47.png)

Um evento relacionado com o registo de autenticação de multifactor azure é mostrado aqui:

![Registos de autenticação de vários fatores Azure](./media/howto-mfa-nps-extension-vpn/image48.png)

Para efazer resolução avançada de problemas, consulte os ficheiros de registo de formato de base de dados NPS onde o serviço NPS está instalado. Os ficheiros de registo são criados na pasta _%SystemRoot%\System32\Logs_ como ficheiros de texto delimitados em vírem. Para obter uma descrição dos ficheiros de registo, consulte [Interprete ficheiros](https://technet.microsoft.com/library/cc771748.aspx)de registo de formato de base de dados NPS .

As entradas nestes ficheiros de registo são difíceis de interpretar a menos que exporte-as para uma folha de cálculo ou uma base de dados. Pode encontrar muitas ferramentas de análise do Serviço de Autenticação da Internet (IAS) online para o ajudar a interpretar os ficheiros de registo. A saída de uma [dessas aplicações](https://www.deepsoftware.com/iasviewer) de shareware descarregadas é mostrada aqui:

![Parser iAS app Shareware da amostra](./media/howto-mfa-nps-extension-vpn/image49.png)

Para fazer uma resolução adicional de problemas, pode utilizar um analisador de protocolos, como o Wireshark ou [o Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). A seguinte imagem da Wireshark mostra as mensagens RADIUS entre o servidor VPN e o NPS.

![Microsoft Message Analyzer mostrando tráfego filtrado](./media/howto-mfa-nps-extension-vpn/image50.png)

Para mais informações, consulte [Integrar a sua infraestrutura de NPS existente com autenticação multi-factor Azure](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Passos seguintes

[Obtenha autenticação multi-factor Azure](concept-mfa-licensing.md)

[Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS](howto-mfaserver-nps-rdg.md)

[Integrar os diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
