---
title: Integrar RDG com extensão Azure MFA NPS - Diretório Ativo Azure
description: Integre a sua infraestrutura remote Desktop Gateway com o Azure MFA utilizando a extensão do Servidor de Política de Rede para o Microsoft Azure
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
ms.openlocfilehash: c61bea7f3ca1105edfec54501c5f0725a5a10225
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80654102"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integre a sua infraestrutura de gateway de ambiente de trabalho remoto utilizando a extensão do Servidor de Política de Rede (NPS) e a AD Azure

Este artigo fornece detalhes para integrar a sua infraestrutura de Gateway de Ambiente de Trabalho Remoto com a Autenticação De Múltiplos Fatores Azure (MFA) utilizando a extensão do Servidor de Política de Rede (NPS) para o Microsoft Azure.

A extensão do Servidor de Política de Rede (NPS) para o Azure permite que os clientes protejam a autenticação do cliente do Serviço de Utilizador de Autenticação Remota (RADIUS) utilizando a [autenticação multi-factor baseada](multi-factor-authentication.md)na nuvem do Azure (MFA) . Esta solução fornece uma verificação em duas etapas para adicionar uma segunda camada de segurança aos insines e transações dos utilizadores.

Este artigo fornece instruções passo a passo para integrar a infraestrutura NPS com o Azure MFA utilizando a extensão NPS para o Azure. Isto permite uma verificação segura para os utilizadores que tentam iniciar sessão num Gateway de Ambiente de Trabalho Remoto.

> [!NOTE]
> Este artigo não deve ser utilizado com implementações do MFA Server e deve ser utilizado apenas com implementações de MFA Azure (baseada em Nuvem).

A Política de Rede e serviços de acesso (NPS) dá às organizações a capacidade de fazer o seguinte:

* Defina localizações centrais para a gestão e controlo dos pedidos de rede especificando quem pode ligar, quais as horas do dia de ligações, a duração das ligações e o nível de segurança que os clientes devem usar para ligar, e assim por diante. Em vez de especificar estas políticas em cada servidor de Gateway VPN ou Remote Desktop (RD), estas políticas podem ser especificadas uma vez numa localização central. O protocolo RADIUS prevê a Autenticação Centralizada, Autorização e Contabilidade (AAA).
* Estabeleça e aplique políticas de saúde dos clientes de Proteção de Acesso à Rede (NAP) que determinam se os dispositivos têm acesso ilimitado ou restrito aos recursos da rede.
* Forneça um meio para impor a autenticação e autorização de acesso a 802.1x pontos de acesso sem fios capazes de acesso e comutadores Ethernet.

Tipicamente, as organizações usam NPS (RADIUS) para simplificar e centralizar a gestão das políticas vpn. No entanto, muitas organizações também usam NPS para simplificar e centralizar a gestão das Políticas de Autorização de Ligação de Desktop RD (RD CAPs).

As organizações também podem integrar o NPS com o Azure MFA para reforçar a segurança e fornecer um alto nível de conformidade. Isto ajuda a garantir que os utilizadores estabeleçam uma verificação em duas etapas para iniciar sessão no Gateway remote desktop. Para que os utilizadores tenham acesso, devem fornecer o seu nome de utilizador/combinação de palavra-passe, juntamente com informações que o utilizador tem sob o seu controlo. Estas informações devem ser fidedignas e não facilmente duplicadas, como um número de telemóvel, número fixo, aplicação num dispositivo móvel, e assim por diante. ATUALMENTE, o RDG suporta notificações de chamadas telefónicas e push dos métodos de aplicação do autenticador microsoft para 2FA. Para obter mais informações sobre métodos de autenticação suportados consulte a secção Determine quais os métodos de [autenticação que os seus utilizadores podem utilizar](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Antes da disponibilidade da extensão NPS para o Azure, os clientes que pretendiam implementar uma verificação em duas etapas para ambientes Integrados de NPS e MFA Azure tiveram de configurar e manter um Servidor MFA separado no ambiente no local, conforme documentado no [Remote Desktop Gateway e no Azure Multi-Factor Authentication Server utilizando](howto-mfaserver-nps-rdg.md)o RADIUS .

A disponibilidade da extensão NPS para o Azure dá agora às organizações a opção de implementar uma solução MFA baseada no local ou uma solução MFA baseada na nuvem para garantir a autenticação do cliente RADIUS.

## <a name="authentication-flow"></a>Fluxo de Autenticação

Para que os utilizadores tenham acesso aos recursos de rede através de um Gateway de Ambiente de Trabalho Remoto, devem cumprir as condições especificadas numa Política de Autorização de Ligação RD (RD CAP) e numa Política de Autorização de Recursos RD (RD RAP). OS CAPs rd especificam quem está autorizado a ligar-se a Gateways RD. Os RAPs RD especificam os recursos de rede, tais como desktops remotos ou aplicações remotas, aos quais o utilizador está autorizado a ligar-se através do Portal RD.

Um Gateway RD pode ser configurado para usar uma loja de política central para CAPs RD. Os RAPs rd não podem usar uma política central, uma vez que são processados no Portal RD. Um exemplo de um Portal RD configurado para usar uma loja de política central para CAPs RD é um cliente RADIUS para outro servidor NPS que serve como a loja de política central.

Quando a extensão NPS para Azure é integrada com o NPS e O Gateway remote desktop, o fluxo de autenticação bem sucedido é o seguinte:

1. O servidor Remote Desktop Gateway recebe um pedido de autenticação de um utilizador de ambiente de trabalho remoto para se ligar a um recurso, como uma sessão de ambiente de trabalho remoto. Atuando como um cliente RADIUS, o servidor Remote Desktop Gateway converte o pedido para uma mensagem radius Access-Request e envia a mensagem para o servidor RADIUS (NPS) onde a extensão NPS está instalada.
1. A combinação de nome de utilizador e palavra-passe é verificada no Diretório Ativo e o utilizador é autenticado.
1. Se todas as condições especificadas no Pedido de Ligação NPS e nas Políticas de Rede forem satisfeitas (por exemplo, restrições de adesão à hora do dia ou do grupo), a extensão nps desencadeia um pedido de autenticação secundária com o Azure MFA.
1. O Azure MFA comunica com a Azure AD, recupera os detalhes do utilizador e realiza a autenticação secundária utilizando métodos suportados.
1. Após o sucesso do desafio MFA, o Azure MFA comunica o resultado à extensão nps.
1. O servidor NPS, onde a extensão é instalada, envia uma mensagem RADIUS Access-Accept para a política RD CAP para o servidor Gateway remote desktop.
1. O utilizador tem acesso ao recurso de rede solicitado através do Portal RD.

## <a name="prerequisites"></a>Pré-requisitos

Esta secção detalha os pré-requisitos necessários antes de integrar o Azure MFA com o Gateway de Ambiente de Trabalho Remoto. Antes de começar, deve ter os seguintes pré-requisitos no lugar.  

* Infraestrutura de Serviços de Ambiente de Trabalho Remoto (RDS)
* Licença Azure MFA
* Software Windows Server
* Função de Política de Rede e Serviços de Acesso (NPS)
* Diretório Ativo Azure sincronizado com diretório ativo no local
* Id guia do diretório ativo Azure

### <a name="remote-desktop-services-rds-infrastructure"></a>Infraestrutura de Serviços de Ambiente de Trabalho Remoto (RDS)

Deve ter uma infraestrutura de Serviços de Ambiente de Trabalho Remoto (RDS) em funcionamento. Se não o fizer, poderá criar rapidamente esta infraestrutura em Azure utilizando o seguinte modelo de arranque rápido: Criar a implementação da Recolha de [Sessões](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment)de Ambiente de Trabalho Remota .

Se desejar criar manualmente uma infraestrutura RDS no local rapidamente para efeitos de teste, siga os passos para implementar uma.
**Saiba mais:** [Implante RDS com quickstart Azure](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) e implantação básica de [infraestruturas RDS.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)

### <a name="azure-mfa-license"></a>Licença Azure MFA

É necessária uma licença para o Azure MFA, que está disponível através do Azure AD Premium ou de outros pacotes que o incluem. As licenças baseadas no consumo para o Azure MFA, tais como por utilizador ou por licençade autenticação, não são compatíveis com a extensão NPS. Para mais informações, consulte Como obter a [autenticação de multi-factors Azure](concept-mfa-licensing.md). Para efeitos de teste, pode utilizar uma subscrição experimental.

### <a name="windows-server-software"></a>Software Windows Server

A extensão NPS requer o Windows Server 2008 R2 SP1 ou superior com o serviço de função NPS instalado. Todos os passos nesta secção foram realizados usando o Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Função de Política de Rede e Serviços de Acesso (NPS)

O serviço de funções NPS fornece o servidor RADIUS e a funcionalidade do cliente, bem como o serviço de saúde da Política de Acesso à Rede. Esta função deve ser instalada em pelo menos dois computadores na sua infraestrutura: O Gateway remote desktop e outro servidor ou controlador de domínio membro. Por predefinição, a função já está presente no computador configurado como gateway remote desktop.  Também deve instalar a função NPS em, pelo menos, noutro computador, como um controlador de domínio ou um servidor membro.

Para obter informações sobre a instalação do serviço de role service NPS Windows Server 2012 ou mais, consulte Instalar um Servidor de Política de [Saúde NAP](https://technet.microsoft.com/library/dd296890.aspx). Para uma descrição das melhores práticas para nPS, incluindo a recomendação de instalar NPS num controlador de domínio, consulte [As Melhores Práticas para NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Diretório Ativo Azure sincronizado com diretório ativo no local

Para utilizar a extensão NPS, os utilizadores no local devem ser sincronizados com a AD Azure e ativados para MFA. Esta secção pressupõe que os utilizadores no local são sincronizados com a AD Azure utilizando o AD Connect. Para obter informações sobre a ligação da AD Azure, consulte [Integrar os seus diretórios no local com o Diretório Ativo Azure](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Id guia do diretório ativo Azure

Para instalar a extensão NPS, é necessário conhecer o GUID do AD Azure. As instruções para encontrar o GUID do AD Azure são fornecidas abaixo.

## <a name="configure-multi-factor-authentication"></a>Configurar a autenticação de vários fatores

Esta secção fornece instruções para integrar o Azure MFA com o Gateway de Ambiente de Trabalho Remoto. Como administrador, tem de configurar o serviço Azure MFA antes de os utilizadores poderem auto-registar os seus dispositivos ou aplicações multifactor.

Siga os passos em [Iniciar-se com a Autenticação Multi-Factor Azure na nuvem](howto-mfa-getstarted.md) para ativar o MFA para os seus utilizadores de AD Azure.

### <a name="configure-accounts-for-two-step-verification"></a>Configure as contas para a verificação em duas etapas

Uma vez que uma conta tenha sido ativada para o MFA, não pode assinar recursos regidos pela política de MFA até que tenha configurado com sucesso um dispositivo de confiança para usar para o segundo fator de autenticação e tenha autenticado usando a verificação em duas etapas.

Siga os passos em O que significa a [autenticação multi-factor azure para mim?](../user-help/multi-factor-authentication-end-user.md)

## <a name="install-and-configure-nps-extension"></a>Instalar e configurar extensão nPS

Esta secção fornece instruções para configurar a infraestrutura RDS para utilizar o Azure MFA para autenticação do cliente com o Gateway de Ambiente de Trabalho Remoto.

### <a name="acquire-azure-active-directory-guid-id"></a>Adquirir id GUID de Diretório Ativo Azure

Como parte da configuração da extensão NPS, você precisa fornecer credenciais de administração e o ID Azure AD para o seu inquilino Azure AD. Os seguintes passos mostram-lhe como obter a identificação do inquilino.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global do inquilino Azure.
1. No menu do portal Azure, selecione **Azure Ative Directory,** ou procure e selecione **Azure Ative Directory** a partir de qualquer página.
1. Selecione **Propriedades**.
1. Na lâmina Propriedades, ao lado do ID do Diretório, clique no ícone **Copy,** como mostrado abaixo, para copiar o ID para a área de cópia.

   ![Obtenção do ID do Diretório do portal Azure](./media/howto-mfa-nps-extension-rdg/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>Instale a extensão NPS

Instale a extensão NPS num servidor que tenha a função Política de Rede e Serviços de Acesso (NPS) instalada. Isto funciona como o servidor RADIUS para o seu design.

> [!Important]
> Certifique-se de que não instala a extensão NPS no seu servidor Gateway de Ambiente de Trabalho Remoto.
>

1. Descarregue a [extensão NPS](https://aka.ms/npsmfa).
1. Copie o ficheiro executável de configuração (NpsExtnForAzureMfaInstaller.exe) para o servidor NPS.
1. No servidor NPS, clique duas vezes **npsExtnForAzureMfaInstaller.exe**. Se solicitado, clique em **Executar**.
1. Na caixa de diálogo de configuração De Configuração NPS Para Azure MFA, reveja os termos da licença de software, verifique **se concordo com os termos e condições da licença**e clique em **Instalar**.
1. Na extensão NPS Para a caixa de diálogo de configuração Azure MFA, clique em **Fechar**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configure certificados para utilização com a extensão NPS utilizando um script PowerShell

Em seguida, é necessário configurar certificados para utilização através da extensão NPS para garantir comunicações seguras e garantias. Os componentes NPS incluem um script Windows PowerShell que configura um certificado auto-assinado para uso com NPS.

O guião executa as seguintes ações:

* Cria um certificado auto-assinado
* Associados chave pública de certificado para diretor de serviço em Azure AD
* Armazena o cert na loja de máquinas local
* Concede acesso à chave privada do certificado ao utilizador da rede
* Reinicia o serviço de servidor de política de rede

Se quiser utilizar os seus próprios certificados, tem de associar a chave pública do seu certificado ao diretor de serviço da Azure AD, e assim por diante.

Para utilizar o script, forneça a extensão com as suas credenciais de administrador AD Azure e o ID de inquilino Azure AD que copiou anteriormente. Execute o script em cada servidor NPS onde instalou a extensão NPS. Em seguida, faça o seguinte:

1. Abra um pedido administrativo do Windows PowerShell.
1. No pedido powerShell, `cd 'c:\Program Files\Microsoft\AzureMfa\Config'`digite e prima **ENTER**.
1. Digite, `.\AzureMfaNpsExtnConfigSetup.ps1`pressione **ENTER**. O script verifica se o módulo PowerShell do Diretório Ativo Azure está instalado. Se não for instalado, o script instala o módulo para si.

   ![Execução AzureMfaNpsExtnConfigSetup.ps1 em Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Depois de o script verificar a instalação do módulo PowerShell, exibe a caixa de diálogo do módulo PowerShell do Diretório Ativo Azure. Na caixa de diálogo, introduza as suas credenciais de administração e senha de anúncio sinuoso E clique em **'Iniciar' o 'Sign'.**

   ![Autenticação à Azure AD na PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Quando solicitado, colhe o ID do Diretório que copiou para a pasta mais cedo e prima **ENTER**.

   ![Inserir o ID do Diretório na PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. O script cria um certificado auto-assinado e executa outras alterações de configuração. A saída deve ser como a imagem mostrada abaixo.

   ![Saída de PowerShell mostrando certificado auto-assinado](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Configure os componentes nPS no Gateway do Ambiente de Trabalho Remoto

Nesta secção, configura as políticas de autorização de ligação remote Desktop Gateway e outras definições RADIUS.

O fluxo de autenticação requer que as mensagens RADIUS sejam trocadas entre o Gateway Remote Desktop e o servidor NPS onde a extensão NPS está instalada. Isto significa que deve configurar as definições do cliente RADIUS tanto no Gateway Remote Desktop como no servidor NPS onde a extensão NPS está instalada.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Configure as políticas de autorização de ligação de gateway de ambiente de trabalho remoto para usar a loja central

As políticas de autorização de ligação de ambiente de trabalho remoto (RD CAPs) especificam os requisitos para a ligação a um servidor de Gateway de Ambiente de Trabalho Remoto. Os CAPs RD podem ser armazenados localmente (por defeito) ou podem ser armazenados numa loja central RD CAP que esteja a funcionar NPS. Para configurar a integração do Azure MFA com RDS, é necessário especificar a utilização de uma loja central.

1. No servidor RD Gateway, abra **o Server Manager**.
1. No menu, clique em **Ferramentas,** aponte para **serviços de ambiente**de trabalho remotos e, em seguida, clique em **Remote Desktop Gateway Manager**.
1. No Gestor de Gateway RD, clique no **Properties** ** \[nome\] do servidor (Local)** e clique em Propriedades .
1. Na caixa de diálogo Properties, selecione o **separador RD CAP Store.**
1. No separador RD CAP Store, selecione **Central server running NPS**. 
1. No nome ou endereço IP para o campo **NPS do servidor,** digite o endereço IP ou o nome do servidor onde instalou a extensão NPS.

   ![Insira o nome ou endereço IP do seu Servidor NPS](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Clique em **Adicionar**.
1. Na caixa de diálogo **Shared Secret,** introduza um segredo partilhado e clique em **OK**. Certifique-se de que regista este segredo partilhado e guarde o registo de forma segura.

   >[!NOTE]
   >O segredo partilhado é usado para estabelecer confiança entre os servidores radius e os clientes. Crie um longo e complexo segredo.
   >

   ![Criar um segredo partilhado para estabelecer confiança](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Clique em **OK** para fechar a caixa de diálogo.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Configure o valor do tempo de tempo no Gateway De desktop remoto NPS

Para garantir que há tempo para validar as credenciais dos utilizadores, realizar verificação em duas etapas, receber respostas e responder às mensagens RADIUS, é necessário ajustar o valor de tempo de tempo DEStituto de RAIO.

1. No servidor RD Gateway, abra o Server Manager. No menu, clique em **Ferramentas,** e depois clique no Servidor de **Política de Rede**.
1. Na consola **NPS (Local),** expanda **clientes e servidores RADIUS,** e selecione **Remote RADIUS Server**.

   ![Consola de gestão do Servidor de Política de Rede mostrando o servidor RADIUS remoto](./media/howto-mfa-nps-extension-rdg/image12.png)

1. No painel de detalhes, clique duas **vezes no TS GATEWAY SERVER GROUP**.

   >[!NOTE]
   >Este Radius Server Group foi criado quando configurao o servidor central para as políticas de NPS. O PORTAL RD reencaminha mensagens RADIUS para este servidor ou grupo de servidores, se mais de um no grupo.
   >

1. Na caixa de diálogo **TS GATEWAY SERVER Properties,** selecione o endereço IP ou o nome do servidor NPS configurado para armazenar CAPs RD e, em seguida, clique em **Editar**.

   ![Selecione o IP ou o nome do Servidor NPS configurado anteriormente](./media/howto-mfa-nps-extension-rdg/image13.png)

1. Na caixa de diálogo **'Servidor',** selecione o separador Balanceamento de **Carga.**
1. No separador **Balanceando de Carga,** no número de segundos sem resposta antes de ser considerado campo **de suposição,** altere o valor predefinido de 3 para um valor entre 30 e 60 segundos.
1. No Número de segundos entre pedidos quando o servidor é identificado como campo **indisponível,** altere o valor predefinido de 30 segundos para um valor igual ou superior ao valor especificado no passo anterior.

   ![Editar definições de tempo de tempo do Servidor radius no separador de equilíbrio de carga](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Clique em **OK** duas vezes para fechar as caixas de diálogo.

### <a name="verify-connection-request-policies"></a>Verificar políticas de pedido de ligação

Por predefinição, quando configura rP gateway para utilizar uma loja de política central para políticas de autorização de ligação, o Gateway RD é configurado para retransmitir pedidos de CAP para o servidor NPS. O servidor NPS com a extensão Azure MFA instalada, processa o pedido de acesso RADIUS. Os seguintes passos mostram-lhe como verificar a política de pedido de ligação por defeito.  

1. No Portal RD, na consola NPS (Local), expanda **políticas**e selecione Políticas de Pedido de **Ligação**.
1. Política de **AUTORIZAÇÃO DE GATEWAY de**dois cliques.
1. Na caixa de diálogo de propriedades de autorização de **GATEWAY TS,** clique no separador **Definições.**
1. No separador **Definições,** sob pedido de ligação de reencaminhamento, clique em **Autenticação**. O cliente RADIUS está configurado para encaminhar pedidos de autenticação.

   ![Configurar Definições de Autenticação especificando o grupo do servidor](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Clique **em Cancelar**.

>[!NOTE]
> Para obter mais informações sobre a criação de uma política de pedido de conexão consulte o artigo, [Configure documentação](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) de políticas de pedido de conexão para o mesmo. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Configure o NPS no servidor onde a extensão NPS está instalada

O servidor NPS onde a extensão NPS está instalada precisa de ser capaz de trocar mensagens RADIUS com o servidor NPS no Gateway remote desktop. Para permitir esta troca de mensagens, é necessário configurar os componentes NPS no servidor onde o serviço de extensão NPS está instalado.

### <a name="register-server-in-active-directory"></a>Registar servidor em Diretório Ativo

Para funcionar corretamente neste cenário, o servidor NPS precisa de ser registado no Diretório Ativo.

1. No servidor NPS, abra **o Server Manager**.
1. No Gestor do Servidor, clique em **Ferramentas**e, em seguida, clique em Servidor de Política de **Rede**.
1. Na consola do Servidor de Política de Rede, clique no **NPS (Local)** e clique **no servidor Register no Diretório Ativo**.
1. Clique **ok** duas vezes.

   ![Registe o servidor NPS em Diretório Ativo](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Deixe a consola aberta para o próximo procedimento.

### <a name="create-and-configure-radius-client"></a>Criar e configurar o cliente RADIUS

O Gateway Remote Desktop precisa de ser configurado como um cliente RADIUS para o servidor NPS.

1. No servidor NPS onde a extensão NPS está instalada, na consola **NPS (Local),** clique à direita nos **Clientes RADIUS** e clique em **New**.

   ![Criar um novo cliente RADIUS na consola NPS](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Na caixa de diálogo new **RADIUS Client,** forneça um nome amigável, como _Gateway,_ e o endereço IP ou nome DNS do servidor Gateway Remote Desktop.
1. No **segredo partilhado** e no **Confirm partilharam** campos secretos, insira o mesmo segredo que usou antes.

   ![Configure um nome amigável e o endereço IP ou DNS](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Clique **em OK** para fechar a caixa de diálogo do Cliente New RADIUS.

### <a name="configure-network-policy"></a>Configure política de rede

Lembre-se que o servidor NPS com a extensão Azure MFA é o local de política central designado para a Política de Autorização de Ligação (CAP). Por isso, é necessário implementar um CAP no servidor NPS para autorizar pedidos de ligações válidos.  

1. No Servidor NPS, abra a consola NPS (Local), expanda **políticas**e clique em **Políticas de Rede**.
1. Clique à direita **Ligações para outros servidores**de acesso e clique na **Política duplicada**.

   ![Duplicar a ligação a outras políticas de servidores de acesso](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Clique direito **Copiar de Ligações para outros servidores**de acesso, e clique em **Propriedades**.
1. Na caixa de diálogo **copy of Connections para outros servidores** de acesso, em nome de **Política,** introduza um nome adequado, como _RDG_CAP_. Verifique a **política ativada**e selecione **o acesso ao Grant**. Opcionalmente, no **tipo de servidor de acesso à rede,** selecione Remote Desktop **Gateway,** ou pode deixá-lo como **não especificado**.

   ![Nomear a política, permitir e conceder acesso](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Clique no separador **Restrições** e verifique **Permitir que os clientes se conectem sem negociar um método**de autenticação .

   ![Modificar métodos de autenticação para permitir que os clientes se conectem](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Opcionalmente, clique no separador **Condições** e adicione condições que devem ser satisfeitas para que a ligação seja autorizada, por exemplo, a adesão num grupo windows específico.

   ![Especificar opcionalmente as condições de ligação](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Clique em **OK**. Quando for solicitado a visualizar o tópico de ajuda correspondente, clique **em No**.
1. Certifique-se de que a sua nova política está no topo da lista, que a política está ativada e que concede acesso.

   ![Mova a sua política para o topo da lista](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Verificar a configuração

Para verificar a configuração, tem de iniciar sessão no Gateway Remote Desktop com um cliente RDP adequado. Certifique-se de que utiliza uma conta que é permitida pelas suas Políticas de Autorização de Ligação e está ativada para O MFA Azure.

Como mostra na imagem abaixo, pode utilizar a página **remote Desktop Web Access.**

![Teste no acesso à Web do ambiente de trabalho remoto](./media/howto-mfa-nps-extension-rdg/image25.png)

Ao introduzir com sucesso as suas credenciais para autenticação primária, a caixa de diálogo Remote Desktop Connect mostra um estado de ligação remota de iniciação, como mostrado abaixo. 

Se autenticar com sucesso com o método de autenticação secundária configurado anteriormente no Azure MFA, está ligado ao recurso. No entanto, se a autenticação secundária não for bem sucedida, é-lhe negado o acesso ao recurso. 

![Ligação remota de ambiente de trabalho com início de uma ligação remota](./media/howto-mfa-nps-extension-rdg/image26.png)

No exemplo abaixo, a aplicação Autenticadora num telefone Windows é utilizada para fornecer a autenticação secundária.

![Exemplo, aplicativo de autenticador de telefone do Windows mostrando verificação](./media/howto-mfa-nps-extension-rdg/image27.png)

Uma vez autenticado com sucesso utilizando o método de autenticação secundária, está registado no Gateway Remote Desktop, normalmente. No entanto, como é obrigado a utilizar um método de autenticação secundária utilizando uma aplicação móvel num dispositivo de confiança, o sinal em processo é mais seguro do que seria de outra forma.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Ver registos do Espectador de Eventos para eventos de logon bem sucedidos

Para visualizar os eventos de acesso bem-sucedidos nos registos do Windows Event Viewer, pode emitir o seguinte comando Do Windows PowerShell para consultar os serviços de terminal windows e registos de Segurança do Windows.

Para consultar eventos de entrada bem sucedidos nos registos operacionais gateway _(Registos de Visualização de Eventos\Aplicações e Serviços\Microsoft\Windows\TerminalServices-Gateway\Operational)_, utilize os seguintes comandos PowerShell:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Este comando exibe eventos Windows que mostram que o utilizador cumpriu os requisitos da política de autorização de recursos (RD RAP) e foi-lhe concedido acesso.

![Visualização de eventos usando PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Este comando exibe os eventos que mostram quando o utilizador cumpriu os requisitos da política de autorização de ligação.

![visualização da política de autorização de ligação utilizando o PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Também pode ver este registo e filtro em IDs de eventos, 300 e 200. Para consultar eventos de logon bem sucedidos nos registos do espectador do evento de segurança, utilize o seguinte comando:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Este comando pode ser executado no NPS central ou no Servidor RD Gateway.

![Experimente eventos de logon bem sucedidos](./media/howto-mfa-nps-extension-rdg/image30.png)

Também pode ver o registo de segurança ou a visão personalizada da Política de Rede e serviços de acesso, como mostrado abaixo:

![Política de Rede e Espectador de Eventos de Acesso](./media/howto-mfa-nps-extension-rdg/image31.png)

No servidor onde instalou a extensão NPS para O MFA do Azure, pode encontrar registos específicos da aplicação do Observador de Eventos específicos da extensão em Registos de _Aplicação e Serviços\Microsoft\AzureMfa_.

![Registos de aplicações do Espectador de Eventos AuthZ](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Guia de resolução de problemas

Se a configuração não estiver a funcionar como esperado, o primeiro local para começar a resolução de problemas é verificar se o utilizador está configurado para utilizar o Azure MFA. Mande o utilizador ligar-se ao [portal Azure](https://portal.azure.com). Se os utilizadores forem solicitados para verificação secundária e puderem autenticar com sucesso, pode eliminar uma configuração incorreta do Azure MFA.

Se o Azure MFA estiver a trabalhar para o(s utilizador's), deverá rever os registos do Evento relevantes. Estes incluem os registos de Segurança, Gateway operacional e Azure MFA que são discutidos na secção anterior.

Abaixo está uma saída de exemplo do registo de segurança mostrando um evento de logon falhado (Id do evento 6273).

![Amostra de um evento de logon falhado](./media/howto-mfa-nps-extension-rdg/image33.png)

Abaixo está um evento relacionado a partir dos registos AzureMFA:

![Sample Azure MFA log in Event Viewer](./media/howto-mfa-nps-extension-rdg/image34.png)

Para executar opções avançadas de resolução de problemas, consulte os ficheiros de registo de formato de base de dados NPS onde o serviço NPS está instalado. Estes ficheiros de registo são criados na pasta _%SystemRoot%\System32\Logs_ como ficheiros de texto delimitados em vírem.

Para obter uma descrição destes ficheiros de registo, consulte [Interprete ficheiros](https://technet.microsoft.com/library/cc771748.aspx)de registo de formato de base de dados NPS . As entradas nestes ficheiros de registo podem ser difíceis de interpretar sem as importar numa folha de cálculo ou numa base de dados. Pode encontrar vários parsers IAS online para ajudá-lo a interpretar os ficheiros de registo.

A imagem abaixo mostra a saída de uma [dessas aplicações](https://www.deepsoftware.com/iasviewer)de shareware descartáveis.

![Parser iAS app Shareware da amostra](./media/howto-mfa-nps-extension-rdg/image35.png)

Finalmente, para opções adicionais de resolução de problemas, pode utilizar um analisador de protocolo, como o [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

A imagem abaixo do Microsoft Message Analyzer mostra o tráfego de rede filtrado no protocolo RADIUS que contém o nome de utilizador **CONTOSO\AliceC**.

![Microsoft Message Analyzer mostrando tráfego filtrado](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Passos seguintes

[How to get Azure Multi-Factor Authentication](concept-mfa-licensing.md) (Como obter a Multi-Factor Authentication do Azure)

[Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS](howto-mfaserver-nps-rdg.md)

[Integrar os diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
