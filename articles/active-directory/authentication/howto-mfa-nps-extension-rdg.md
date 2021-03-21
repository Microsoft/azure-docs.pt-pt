---
title: Integre o RDG com extensão AZURE AD MFA NPS - Azure Ative Directory
description: Integre a sua infraestrutura Remote Desktop Gateway com Azure AD MFA utilizando a extensão do Servidor de Política de Rede para o Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b0150c18f2c007ed104d34daacd49ab03131a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743382"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integre a sua infraestrutura Remote Desktop Gateway utilizando a extensão do Network Policy Server (NPS) e a AZure AD

Este artigo fornece detalhes para integrar a sua infraestrutura Remote Desktop Gateway com autenticação multi-factor AD (MFA) a utilizar a extensão do Network Policy Server (NPS) para o Microsoft Azure.

A extensão do Servidor de Política de Rede (NPS) para o Azure permite que os clientes protejam a autenticação do cliente do Serviço de Acesso ao Utilizador (RADIUS) de autenticação remota utilizando [a autenticação multi-factor (MFA)](./concept-mfa-howitworks.md)da Azure na nuvem. Esta solução fornece uma verificação em duas etapas para adicionar uma segunda camada de segurança às entradas e transações do utilizador.

Este artigo fornece instruções passo a passo para a integração da infraestrutura NPS com Azure AD MFA utilizando a extensão NPS para Azure. Isto permite uma verificação segura para os utilizadores que tentam iniciar sing a um Gateway de Secretária remoto.

> [!NOTE]
> Este artigo não deve ser utilizado com implementações do MFA Server e só deve ser utilizado com implementações Azure AD MFA (baseadas em nuvem).

A Política de Rede e Serviços de Acesso (NPS) confere às organizações a capacidade de fazer o seguinte:

* Defina as localizações centrais para a gestão e controlo de pedidos de rede, especificando quem pode ligar, que horas são permitidas as ligações do dia, a duração das ligações e o nível de segurança que os clientes devem utilizar para se conectarem, e assim por diante. Em vez de especificar estas políticas em cada servidor gateway VPN ou Remote Desktop (RD), estas políticas podem ser especificadas uma vez num local central. O protocolo RADIUS fornece a Autenticação Centralizada, Autorização e Contabilidade (AAA).
* Estabeleça e aplique políticas de saúde do cliente de Proteção de Acesso à Rede (NAP) que determinam se os dispositivos são concedidos sem restrições ou acesso restrito aos recursos de rede.
* Forneça um meio para impor a autenticação e autorização de acesso a pontos de acesso sem fios com capacidade 802.1x e comutadores Ethernet.

Tipicamente, as organizações usam NPS (RADIUS) para simplificar e centralizar a gestão das políticas VPN. No entanto, muitas organizações também usam NPS para simplificar e centralizar a gestão das Políticas de Autorização de Ligação de Ambiente de Trabalho RD (RD CAPs).

As organizações também podem integrar NPS com Azure AD MFA para aumentar a segurança e fornecer um alto nível de conformidade. Isto ajuda a garantir que os utilizadores estabeleçam uma verificação em duas etapas para iniciar sinsuposições no Gateway de Ambiente de Trabalho Remoto. Para que os utilizadores tenham acesso, devem fornecer o seu nome de utilizador/combinação de palavra-passe, juntamente com as informações que o utilizador tem no seu controlo. Estas informações devem ser confiáveis e não facilmente duplicadas, tais como um número de telemóvel, número fixo, aplicação num dispositivo móvel, e assim por diante. A RDG suporta atualmente notificações de chamadas telefónicas e push dos métodos de aplicação autenticadores da Microsoft para 2FA. Para obter mais informações sobre métodos de autenticação suportados consulte a secção [Determinar quais os métodos de autenticação que os seus utilizadores podem utilizar.](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)

Antes da disponibilidade da extensão NPS para o Azure, os clientes que pretendiam implementar uma verificação em duas etapas para ambientes integrados de NPS e AD MFA Ad tiveram de configurar e manter um Servidor MFA separado no ambiente no local, como documentado no [Remote Desktop Gateway e no Azure Multi-Factor Authentication Server utilizando o RADIUS](howto-mfaserver-nps-rdg.md).

A disponibilidade da extensão NPS para o Azure dá agora às organizações a opção de implementar uma solução MFA baseada no local ou uma solução MFA baseada na nuvem para garantir a autenticação do cliente RADIUS.

## <a name="authentication-flow"></a>Fluxo de Autenticação

Para que os utilizadores tenham acesso aos recursos de rede através de um Gateway de Desktop Remoto, devem satisfazer as condições especificadas numa Política de Autorização de Ligação RD (RD CAP) e numa Política de Autorização de Recursos RD (RD RAP). OS CAPs RD especificam quem está autorizado a ligar-se aos Gateways RD. RD RAPs especifica os recursos de rede, tais como desktops remotos ou aplicações remotas, a que o utilizador é autorizado a ligar através do Gateway RD.

Um Gateway RD pode ser configurado para usar uma loja de política central para CAPs RD. Os RAPs rd não podem utilizar uma política central, uma vez que são processados no Gateway RD. Um exemplo de um GATEWAY RD configurado para usar uma loja de política central para CAPs RD é um cliente RADIUS para outro servidor NPS que serve como a loja de política central.

Quando a extensão NPS para Azure é integrada com o NPS e o Remote Desktop Gateway, o fluxo de autenticação bem-sucedido é o seguinte:

1. O servidor Remote Desktop Gateway recebe um pedido de autenticação de um utilizador de ambiente de trabalho remoto para se ligar a um recurso, como uma sessão de Desktop Remoto. Atuando como cliente RADIUS, o servidor Remote Desktop Gateway converte o pedido num RADIUS Access-Request mensagem e envia a mensagem para o servidor RADIUS (NPS) onde a extensão NPS está instalada.
1. O nome de utilizador e a combinação de palavra-passe são verificados no Ative Directory e o utilizador é autenticado.
1. Se todas as condições especificadas no Pedido de Conexão NPS e nas Políticas de Rede forem satisfeitas (por exemplo, restrições de data do dia ou de grupo), a extensão NPS desencadeia um pedido de autenticação secundária com Azure AD MFA.
1. O Azure AD MFA comunica com a Azure AD, recupera os dados do utilizador e executa a autenticação secundária utilizando métodos suportados.
1. Após o sucesso do desafio MFA, a Azure AD MFA comunica o resultado à extensão NPS.
1. O servidor NPS, onde a extensão está instalada, envia uma mensagem de Access-Accept RADIUS para a política RD CAP para o servidor Remote Desktop Gateway.
1. O utilizador tem acesso ao recurso de rede solicitado através do Gateway RD.

## <a name="prerequisites"></a>Pré-requisitos

Esta secção detalha os pré-requisitos necessários antes de integrar o Azure AD MFA com o Gateway de Secretária Remoto. Antes de começar, deve ter os seguintes pré-requisitos no lugar.  

* Infraestrutura de Serviços de Ambiente de Trabalho Remoto (RDS)
* Licença Azure AD MFA
* Software windows Server
* Função de Política de Rede e Serviços de Acesso (NPS)
* Azure Ative Directy sincronizado com o Ative Directory no local
* Azure Ative Directory GUID ID

### <a name="remote-desktop-services-rds-infrastructure"></a>Infraestrutura de Serviços de Ambiente de Trabalho Remoto (RDS)

Deve ter uma infraestrutura de Serviços de Secretária Remota (RDS) em funcionamento. Se não o fizer, poderá criar rapidamente esta infraestrutura em Azure utilizando o seguinte modelo de arranque rápido: [Criar uma implementação de Coleção de Sessão de Secretária remota](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Se desejar criar manualmente uma infraestrutura RDS no local rapidamente para efeitos de teste, siga os passos para implantar uma.
**Saiba mais:** [Implementar RDS com quickstart Azure](/windows-server/remote/remote-desktop-services/rds-in-azure) e [implantação básica de infraestruturas RDS](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-ad-mfa-license"></a>Licença Azure AD MFA

Requerer uma licença para Azure AD MFA, que está disponível através do Azure AD Premium ou de outros pacotes que o incluam. As licenças baseadas no consumo para MFA Azure AD, tais como por utilizador ou por licença de autenticação, não são compatíveis com a extensão NPS. Para mais informações, consulte [Como obter autenticação multi-factor Azure AD](concept-mfa-licensing.md). Para efeitos de teste, pode utilizar uma subscrição experimental.

### <a name="windows-server-software"></a>Software windows Server

A extensão NPS requer o Windows Server 2008 R2 SP1 ou superior com o serviço de função NPS instalado. Todos os passos nesta secção foram realizados usando o Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Função de Política de Rede e Serviços de Acesso (NPS)

O serviço de função NPS fornece o servidor RADIUS e a funcionalidade do cliente, bem como o serviço de saúde da Política de Acesso à Rede. Esta função deve ser instalada em pelo menos dois computadores na sua infraestrutura: O Gateway de Secretária remota e outro servidor de membro ou controlador de domínio. Por predefinição, a função já está presente no computador configurado como O Gateway de Ambiente de Trabalho Remoto.  Também deve instalar a função NPS em pelo menos noutro computador, como um controlador de domínio ou servidor de membros.

Para obter informações sobre a instalação do serviço de funções NPS Windows Server 2012 ou mais antigo, consulte [instalar um Servidor de Política de Saúde NAP](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)). Para uma descrição das melhores práticas para nps, incluindo a recomendação de instalar NPS num controlador de domínio, consulte [as Melhores Práticas para NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Ative Directy sincronizado com o Ative Directory no local

Para utilizar a extensão NPS, os utilizadores no local devem ser sincronizados com Azure AD e habilitados para MFA. Esta secção pressupõe que os utilizadores no local estão sincronizados com a Azure AD utilizando o AD Connect. Para obter informações sobre a ligação Azure AD, consulte [Integre os seus diretórios no local com o Azure Ative Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Ative Directory GUID ID

Para instalar a extensão NPS, é necessário conhecer o GUID do Azure AD. Instruções para encontrar o GUID do AD Azure são fornecidas abaixo.

## <a name="configure-multi-factor-authentication"></a>Configurar a autenticação multi-factor

Esta secção fornece instruções para integrar o Azure AD MFA com o Gateway de Secretária Remoto. Como administrador, tem de configurar o serviço Azure AD MFA antes que os utilizadores possam auto-registar os seus dispositivos ou aplicações multi-factor.

Siga os passos em [Começar com a Autenticação Multi-Factor AZure AD na nuvem](howto-mfa-getstarted.md) para ativar o MFA para os seus utilizadores AZure AD.

### <a name="configure-accounts-for-two-step-verification"></a>Configure explica a verificação em duas etapas

Uma vez ativada uma conta para MFA, não pode subscrever os recursos regidos pela política do MFA até que tenha configurado com sucesso um dispositivo de confiança para utilizar para o segundo fator de autenticação e tenha autenticado usando a verificação em duas etapas.

Siga os passos em [O que significa Azure AD Multi-Factor Authentication para mim?](../user-help/multi-factor-authentication-end-user-first-time.md)

> [!IMPORTANT]
> O sinal de comportamento para Remote Desktop Gateway não fornece a opção de introduzir um código de verificação com autenticação multi-factor Ad Azure. Uma conta de utilizador deve ser configurada para verificação de telefone ou a App autenticador da Microsoft com notificações push.
>
> Se um destes dois métodos de autenticação não estiver configurado para um utilizador, não será capaz de completar o desafio de autenticação multi-factor AD Azure e iniciar sação no Gateway de Desktop Remoto.

## <a name="install-and-configure-nps-extension"></a>Instalar e configurar extensão NPS

Esta secção fornece instruções para configurar a infraestrutura RDS para utilizar o Azure AD MFA para a autenticação do cliente com o Gateway de Secretária Remota.

### <a name="acquire-azure-active-directory-tenant-id"></a>Adquirir ID do inquilino do Azure Ative Directory

Como parte da configuração da extensão NPS, você precisa fornecer credenciais de administração e o ID AD Azure para o seu inquilino AZURE AD. Para obter a iD do inquilino, complete os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global do inquilino Azure.
1. No menu do portal Azure, selecione **Azure Ative Directory,** ou procure e selecione **O Diretório Ativo Azure** a partir de qualquer página.
1. Na página **geral,** é mostrada a informação do *Arrendatário.* Ao lado do ID do *inquilino,* selecione o ícone **Copy,** como mostra o seguinte exemplo de imagem:

   ![Obter a ID do Inquilino do portal Azure](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Instale a extensão NPS

Instale a extensão NPS num servidor que tenha a função de Política de Rede e Serviços de Acesso (NPS) instalada. Isto funciona como o servidor RADIUS para o seu design.

> [!IMPORTANT]
> Não instale a extensão NPS no seu servidor Remote Desktop Gateway (RDG). O servidor RDG não utiliza o protocolo RADIUS com o seu cliente, pelo que a extensão não pode interpretar e executar o MFA.
>
> Quando o servidor RDG e o servidor NPS com extensão NPS são servidores diferentes, o RDG utiliza NPS internamente para falar com outros servidores NPS e utiliza o RADIUS como o protocolo para comunicar corretamente.

1. Descarregue a [extensão NPS](https://aka.ms/npsmfa).
1. Copie o ficheiro executável de configuração (NpsExtnForAzureMfaInstaller.exe) para o servidor NPS.
1. No servidor NPS, clique duas **vezesNpsExtnForAzureMfaInstaller.exe**. Se solicitado, clique em **Executar**.
1. Na extensão NPS Para Azure AD MFA Configurar caixa de diálogo, rever os termos da licença de software, verificar **concordo com os termos e condições da licença,** e clique em **Instalar**.
1. Na extensão NPS Para Azure AD MFA Configuração de diálogo, clique em **Fechar**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configure certificados para utilização com a extensão NPS utilizando um script PowerShell

Em seguida, é necessário configurar certificados para utilização pela extensão NPS para garantir comunicações e garantias seguras. Os componentes NPS incluem um script Windows PowerShell que configura um certificado auto-assinado para uso com NPS.

O script executa as seguintes ações:

* Cria um certificado auto-assinado
* Associados chave pública de certificado para o principal serviço na Azure AD
* Armazena o certificado na loja de máquinas local
* Concede acesso à chave privada do certificado ao utilizador da rede
* Reinicia o serviço de servidor de política de rede

Se quiser utilizar os seus próprios certificados, tem de associar a chave pública do seu certificado ao diretor de serviço da Azure AD, e assim por diante.

Para utilizar o script, forneça a extensão com as suas credenciais Azure AD Admin e o ID do inquilino Azure que copiou anteriormente. Execute o script em cada servidor NPS onde instalou a extensão NPS. Em seguida, faça o seguinte:

1. Abra uma solicitação administrativa do Windows PowerShell.
1. Na introdução, tipo e introdução do PowerShell `cd 'c:\Program Files\Microsoft\AzureMfa\Config'` . 
1. Digite `.\AzureMfaNpsExtnConfigSetup.ps1` , e prima **ENTER**. O script verifica se o módulo PowerShell do Diretório Ativo Azure está instalado. Se não for instalado, o script instala o módulo para si.

   ![Executando AzureMfaNpsExtnConfigSetup.ps1 em Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Após o script verificar a instalação do módulo PowerShell, apresenta a caixa de diálogo do módulo powerShell do Azure Ative Directory. Na caixa de diálogo, insira as suas credenciais de administração Admin Ad Azure e senha, e clique **em Iniciar sôm**.

   ![Autenticação para Azure AD em PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Quando solicitado, cole o *ID* do inquilino que copiou para a área de transferência mais cedo, e prima **ENTER**.

   ![Inserindo o ID do inquilino em PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. O script cria um certificado auto-assinado e executa outras alterações de configuração. A saída deve ser como a imagem mostrada abaixo.

   ![Saída de PowerShell mostrando certificado auto-assinado](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Configure componentes NPS em Remote Desktop Gateway

Nesta secção, configura as políticas de autorização de ligação remote desktop Gateway e outras definições DE RADIUS.

O fluxo de autenticação requer que as mensagens RADIUS sejam trocadas entre o Gateway de Ambiente de Trabalho Remoto e o servidor NPS onde a extensão NPS está instalada. Isto significa que deve configurar as definições do cliente RADIUS tanto no Gateway de Ambiente de Trabalho Remoto como no servidor NPS onde a extensão NPS está instalada.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Configure as políticas de autorização de ligação remote desktop Gateway para utilizar a loja central

As políticas de autorização de ligação ao ambiente de trabalho remoto (RD CAPs) especificam os requisitos para a ligação a um servidor Remote Desktop Gateway. Os CAPs RD podem ser armazenados localmente (padrão) ou podem ser armazenados numa loja central de RD CAP que está a executar NPS. Para configurar a integração do Azure AD MFA com RDS, é necessário especificar a utilização de uma loja central.

1. No servidor RD Gateway, abra o **Gestor do Servidor**.
1. No menu, clique em **Ferramentas**, aponte para **Serviços remotos de Desktop** e, em seguida, clique no **Remote Desktop Gateway Manager**.
1. No Gestor de Gateway RD, clique com o botão direito **\[ Nome do Servidor \] (Local)** e clique em **Propriedades**.
1. Na caixa de diálogo Propriedades, selecione o **separador RD CAP Store.**
1. No separador RD CAP Store, selecione **Central server running NPS**. 
1. No **endereço Desnuda a um nome ou endereço IP para o servidor que executa o campo NPS,** digite o endereço IP ou o nome do servidor onde instalou a extensão NPS.

   ![Insira o nome ou endereço IP do seu Servidor NPS](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Clique em **Adicionar**.
1. Na caixa de diálogo **Shared Secret,** insira um segredo partilhado e, em seguida, clique **em OK**. Certifique-se de que grava este segredo partilhado e armazena o disco de forma segura.

   >[!NOTE]
   >O segredo partilhado é usado para estabelecer confiança entre os servidores RADIUS e os clientes. Criar um segredo longo e complexo.
   >

   ![Criar um segredo partilhado para estabelecer confiança](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Clique em **OK** para fechar a caixa de diálogo.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Configure o valor de tempo limite radius no Remote Desktop Gateway NPS

Para garantir que há tempo para validar as credenciais dos utilizadores, realizar verificação em duas etapas, receber respostas e responder às mensagens RADIUS, é necessário ajustar o valor de tempo limite RADIUS.

1. No servidor RD Gateway, abra o Gestor do Servidor. No menu, clique em **Ferramentas** e, em seguida, clique em **'Servidor de Política de Rede'.**
1. Na consola **NPS (Local),** expanda **os clientes e servidores RADIUS** e selecione **O Servidor RADIUS Remoto**.

   ![Consola de gestão de servidor de política de rede mostrando o servidor RADIUS remoto](./media/howto-mfa-nps-extension-rdg/image12.png)

1. No painel de detalhes, clique duas vezes no **Grupo de SERVIDORES TS GATEWAY**.

   >[!NOTE]
   >Este RADIUS Server Group foi criado quando configuraste o servidor central para as políticas de NPS. O GATEWAY RD encaminha mensagens RADIUS para este servidor ou grupo de servidores, se for mais do que um no grupo.
   >

1. Na caixa de diálogo **TS GATEWAY SERVER Group Properties,** selecione o endereço IP ou o nome do servidor NPS configurado para armazenar CAPs RD e, em seguida, clique em **Editar**.

   ![Selecione o IP ou o nome do Servidor NPS configurado anteriormente](./media/howto-mfa-nps-extension-rdg/image13.png)

1. Na caixa de diálogo **do servidor Editar RADIUS,** selecione o **separador de equilíbrio de carga.**
1. No **separador Equilíbrio de Carga,** no Número de segundos sem resposta antes de considerar o **pedido, altere** o valor predefinido de 3 para um valor entre 30 e 60 segundos.
1. No **Número de segundos entre pedidos quando o servidor é identificado como campo indisponível,** altere o valor predefinido de 30 segundos para um valor igual ou superior ao valor especificado no passo anterior.

   ![Editar as definições de tempo limite do Servidor Radius no separador de equilíbrio de carga](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Clique **em OK** duas vezes para fechar as caixas de diálogo.

### <a name="verify-connection-request-policies"></a>Verificar Políticas de Pedido de Ligação

Por predefinição, quando configurar o Gateway RD para utilizar uma loja de política central para políticas de autorização de ligação, o Gateway RD está configurado para encaminhar pedidos de CAP para o servidor NPS. O servidor NPS com a extensão MFA AZure AD instalada, processa o pedido de acesso RADIUS. Os seguintes passos mostram-lhe como verificar a política de pedido de ligação predefinido.  

1. No Gateway RD, na consola NPS (Local), expanda **as políticas** e selecione **Políticas de Pedido de Ligação**.
1. Política de autorização de gateway TS de **duas cliques.**
1. Na caixa de diálogo **de propriedades de propriedades de autorização TS GATEWAY,** clique no **separador Definições.**
1. No **separador Definições,** em Pedido de Ligação de Encaminhamento, clique em **Autenticação**. O cliente RADIUS está configurado para encaminhar pedidos de autenticação.

   ![Configurações de autenticação configurantes especificando o grupo de servidor](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Clique **em Cancelar**.

>[!NOTE]
> Para obter mais informações sobre a criação de uma política de pedido de conexão consulte o artigo, [Configurar a](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) documentação das políticas de pedido de conexão para o mesmo. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Configure NPS no servidor onde a extensão NPS é instalada

O servidor NPS onde está instalada a extensão NPS precisa de ser capaz de trocar mensagens RADIUS com o servidor NPS no Gateway de Ambiente de Trabalho Remoto. Para ativar esta troca de mensagens, é necessário configurar os componentes NPS no servidor onde o serviço de extensão NPS está instalado.

### <a name="register-server-in-active-directory"></a>Registre o servidor no Diretório Ativo

Para funcionar corretamente neste cenário, o servidor NPS precisa de ser registado no Ative Directory.

1. No servidor NPS, abra o **Gestor do Servidor**.
1. No Gestor do Servidor, clique em **Ferramentas** e, em seguida, clique em **'Servidor de Política de Rede'.**
1. Na consola Network Policy Server, clique com o botão direito **NPS (Local)** e, em seguida, clique **no servidor Registar no Ative Directory**.
1. Clique **ok** duas vezes.

   ![Registar o servidor NPS no Ative Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Deixe a consola aberta para o próximo procedimento.

### <a name="create-and-configure-radius-client"></a>Criar e configurar o cliente RADIUS

O Gateway de Ambiente de Trabalho Remoto tem de ser configurado como um cliente RADIUS para o servidor NPS.

1. No servidor NPS onde a extensão NPS está instalada, na consola **NPS (Local),** clique com o botão direito **CLIENTES RADIUS** e clique em **New**.

   ![Criar um novo cliente RADIUS na consola NPS](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Na caixa de diálogo **do cliente RADIUS Novo,** forneça um nome amigável, como _Gateway,_ e o endereço IP ou nome DNS do servidor Remote Desktop Gateway.
1. No **segredo partilhado** e nos campos **secretos partilhados,** insira o mesmo segredo que usou antes.

   ![Configurar um nome amigável e o endereço IP ou DNS](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Clique **em OK** para fechar a caixa de diálogo do novo radius cliente.

### <a name="configure-network-policy"></a>Política de Rede Configure

Lembre-se que o servidor NPS com a extensão Azure AD MFA é a loja de política central designada para a Política de Autorização de Ligação (CAP). Portanto, é necessário implementar uma PAC no servidor NPS para autorizar pedidos de ligações válidos.  

1. No Servidor NPS, abra a consola NPS (Local), expanda **políticas** e clique em **Políticas de Rede**.
1. Clique com o botão direito **Ligações para outros servidores de acesso** e clique em **"Duplicar" Política**.

   ![Duplicar a ligação a outras políticas de servidores de acesso](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Clique com o botão direito **Copiar as ligações para outros servidores de acesso** e clicar em **Propriedades**.
1. Na **cópia de ligações a outros servidores** de acesso, em **nome de Política,** insira um nome adequado, como _RDG_CAP_. Verifique **a Política ativada** e selecione **o acesso ao Grant**. Opcionalmente, no **tipo de servidor de acesso à rede**, selecione Remote Desktop **Gateway**, ou pode deixá-lo como **Não Especificado**.

   ![Nomeie a política, permita e conceda acesso](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Clique no **separador Restrições** e verifique **Se os clientes se conectem sem negociar um método de autenticação.**

   ![Modificar métodos de autenticação para permitir aos clientes conectarem-se](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Opcionalmente, clique no separador **Condições** e adicione condições que devem ser satisfeitas para que a ligação seja autorizada, por exemplo, a aderir a um grupo específico do Windows.

   ![Especificar opcionalmente as condições de ligação](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Clique em **OK**. Quando solicitado para ver o tópico de Ajuda correspondente, clique em **No**.
1. Certifique-se de que a sua nova política está no topo da lista, que a política está ativada e que concede acesso.

   ![Mova a sua política para o topo da lista](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Verificar configuração

Para verificar a configuração, tem de iniciar sing no Remote Desktop Gateway com um cliente RDP adequado. Certifique-se de que utiliza uma conta que é permitida pelas suas Políticas de Autorização de Ligação e está ativada para Azure AD MFA.

Como mostra na imagem abaixo, pode utilizar a página **de Acesso à Web do Ambiente de Trabalho Remoto.**

![Testes no Acesso Web de Desktop Remoto](./media/howto-mfa-nps-extension-rdg/image25.png)

Ao introduzir com sucesso as suas credenciais para a autenticação primária, a caixa de diálogo Remote Desktop Connect mostra um estado de iniciação da ligação remota, como mostrado abaixo. 

Se autenticar com sucesso o método de autenticação secundária que configura anteriormente no Azure AD MFA, está ligado ao recurso. No entanto, se a autenticação secundária não for bem sucedida, é-lhe negado o acesso ao recurso. 

![Ligação remota do ambiente de trabalho iniciando uma ligação remota](./media/howto-mfa-nps-extension-rdg/image26.png)

No exemplo abaixo, a aplicação Authenticator num telefone Windows é utilizada para fornecer a autenticação secundária.

![Exemplo aplicação autenticador de telefone do Windows que mostra a verificação](./media/howto-mfa-nps-extension-rdg/image27.png)

Depois de autenticado com sucesso utilizando o método de autenticação secundária, é normal iniciar sessão no Gateway de Ambiente de Trabalho Remoto. No entanto, uma vez que é obrigado a utilizar um método de autenticação secundária utilizando uma aplicação móvel num dispositivo de confiança, o sinal em processo é mais seguro do que seria de outra forma.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Ver registos do Espectador de Eventos para eventos de início de sessão bem-sucedidos

Para visualizar os eventos de login bem-sucedidos nos registos do Windows Event Viewer, pode emitir o seguinte comando Windows PowerShell para consultar os serviços de terminais do Windows e os registos de Segurança do Windows.

Para consultar eventos de login bem sucedidos nos registos operacionais gateway _(Event Viewer\Applications and Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_, utilize os seguintes comandos PowerShell:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Este comando exibe eventos do Windows que mostram que o utilizador cumpriu os requisitos da política de autorização de recursos (RD RAP) e teve acesso.

![Ver eventos usando PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Este comando exibe os eventos que mostram quando o utilizador cumpriu os requisitos da política de autorização de ligação.

![visualizar a política de autorização de ligação utilizando o PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Também pode ver este registo e filtro nos IDs do evento, 300 e 200. Para consultar eventos de início de sessão bem sucedidos nos registos de visualização do evento security, utilize o seguinte comando:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Este comando pode ser executado no NPS central ou no Servidor RD Gateway.

![Experimente eventos de início de são bem sucedidos](./media/howto-mfa-nps-extension-rdg/image30.png)

Também pode ver o registo de segurança ou a visualização personalizada da Política de Rede e dos Serviços de Acesso, como mostra abaixo:

![Visualizador de eventos de política de rede e serviços de acesso](./media/howto-mfa-nps-extension-rdg/image31.png)

No servidor onde instalou a extensão NPS para Azure AD MFA, pode encontrar registos de aplicações do Event Viewer específicos da extensão nos _Registos de Aplicações e Serviços\Microsoft\AzureMfa_.

![Registos de aplicações do Observador de Eventos AuthZ](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Guia de resolução de problemas

Se a configuração não estiver a funcionar como esperado, o primeiro lugar para começar a resolver problemas é verificar se o utilizador está configurado para utilizar o Azure AD MFA. Ter o utilizador ligado ao [portal Azure](https://portal.azure.com). Se os utilizadores forem solicitados para verificação secundária e puderem autenticar com sucesso, pode eliminar uma configuração incorreta do Azure AD MFA.

Se o Azure AD MFA estiver a trabalhar para os utilizadores, deverá rever os registos de Eventos relevantes. Estes incluem os registos de Security Event, Gateway operacional e Azure AD MFA que são discutidos na secção anterior.

Abaixo está uma saída de exemplo do registo de segurança mostrando um evento de logon falhado (Evento ID 6273).

![Amostra de um evento de logon falhado](./media/howto-mfa-nps-extension-rdg/image33.png)

Abaixo está um evento relacionado a partir dos registos AzureMFA:

![Experimente o registo MFA AD AD da amostra no Espectador de Eventos](./media/howto-mfa-nps-extension-rdg/image34.png)

Para executar opções avançadas de resolução de problemas, consulte os ficheiros de registo de formato de base de dados NPS onde o serviço NPS está instalado. Estes ficheiros de registo são criados na pasta _%SystemRoot%\System32\Logs_ como ficheiros de texto delimitados por vírgula.

Para obter uma descrição destes ficheiros de registo, consulte [os ficheiros de registo do formato de base de dados do NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)). As entradas nestes ficheiros de registo podem ser difíceis de interpretar sem os importar numa folha de cálculo ou numa base de dados. Pode encontrar vários parsers da IAS on-line para ajudá-lo na interpretação dos ficheiros de registo.

A imagem abaixo mostra a saída de uma dessas [aplicações de shareware](https://www.deepsoftware.com/iasviewer)transferíveis .

![Amostra Shareware app IAS parser](./media/howto-mfa-nps-extension-rdg/image35.png)

Finalmente, para opções adicionais de resolução de problemas, pode utilizar um analisador de protocolo, como [o Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide).

A imagem abaixo do Microsoft Message Analyzer mostra o tráfego de rede filtrado no protocolo RADIUS que contém o nome de utilizador **CONTOSO\AliceC**.

![Microsoft Message Analyzer mostrando tráfego filtrado](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Passos seguintes

[Como obter autenticação multi-factor Azure AD](concept-mfa-licensing.md)

[Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS](howto-mfaserver-nps-rdg.md)

[Integrar os diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md)