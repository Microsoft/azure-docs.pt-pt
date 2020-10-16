---
title: Use autenticação multi-factor Azure com NPS - Diretório Ativo Azure
description: Saiba como utilizar as capacidades de autenticação multi-factor Azure com a infraestrutura de autenticação do Servidor de Política de Rede (NPS) existente
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 5095df51fe430990e200b7bc7c3ca03feb0799d5
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964286"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integre a sua infraestrutura existente do Network Policy Server (NPS) com a autenticação multi-factor Azure

A extensão do Network Policy Server (NPS) para autenticação multi-factor Azure adiciona capacidades MFA baseadas na nuvem à sua infraestrutura de autenticação utilizando os servidores existentes. Com a extensão NPS, pode adicionar chamadas telefónicas, mensagens de texto ou verificação de aplicativos telefónicos ao fluxo de autenticação existente sem ter de instalar, configurar e manter novos servidores.

A extensão NPS funciona como um adaptador entre RADIUS e Azure Multi-Factor Authentication baseado na nuvem para fornecer um segundo fator de autenticação para utilizadores federados ou sincronizados.

## <a name="how-the-nps-extension-works"></a>Como funciona a extensão NPS

Quando utiliza a extensão NPS para autenticação multi-factor Azure, o fluxo de autenticação inclui os seguintes componentes:

1. **O SERVIDOR NAS/VPN** recebe pedidos de clientes VPN e converte-os em pedidos RADIUS para servidores NPS.
2. **O NPS Server** conecta-se aos Serviços de Domínio do Diretório Ativo (DS AD) para realizar a autenticação primária para os pedidos RADIUS e, após o sucesso, passa o pedido a quaisquer extensões instaladas.  
3. **A extensão NPS** desencadeia um pedido de autenticação multi-factor Azure para a autenticação secundária. Uma vez que a extensão recebe a resposta, e se o desafio MFA for bem sucedido, completa o pedido de autenticação fornecendo ao servidor NPS fichas de segurança que incluem uma reclamação de MFA, emitida pela Azure STS.
4. **A Azure MFA** comunica com o Azure Ative Directory (Azure AD) para recuperar os dados do utilizador e efetua a autenticação secundária utilizando um método de verificação configurado para o utilizador.

O diagrama que se segue ilustra este fluxo de pedido de autenticação de alto nível:

![Diagrama do fluxo de autenticação para o utilizador autenticando através de um servidor VPN para servidor NPS e a extensão NPS de autenticação multi-factor Azure](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>Comportamento do protocolo RADIUS e a extensão do NPS

Como RADIUS é um protocolo UDP, o remetente assume a perda de pacotes e aguarda uma resposta. Após um período de tempo, a ligação pode esgotar-se. Se assim for, o pacote está ressentido, pois o remetente assume que o pacote não chegou ao destino. No cenário de autenticação neste artigo, os servidores VPN enviam o pedido e aguardam uma resposta. Se a ligação esgotar, o servidor VPN envia novamente o pedido.

![Diagrama do fluxo de pacotes EDP RADIUS e pedidos após o intervalo na resposta do servidor NPS](./media/howto-mfa-nps-extension/radius-flow.png)

O servidor NPS pode não responder ao pedido original do servidor VPN antes do intervalo da ligação, uma vez que o pedido de MFA ainda pode estar a ser processado. O utilizador pode não ter respondido com sucesso à solicitação do MFA, pelo que a extensão NPS de autenticação multi-factor Azure está à espera que o evento esteja concluído. Nesta situação, o servidor NPS identifica pedidos adicionais de servidor VPN como um pedido duplicado. O servidor NPS descarta estes pedidos duplicados de servidor VPN.

![Diagrama do servidor NPS descartando pedidos duplicados do servidor RADIUS](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

Se olhar para os registos do servidor NPS, poderá ver estes pedidos adicionais a serem descartados. Este comportamento é por design para proteger o utilizador final de receber vários pedidos para uma única tentativa de autenticação. Os pedidos descartados no registo de eventos do servidor NPS não indicam que há um problema com o servidor NPS ou com a extensão NPS de autenticação multi-factor Azure.

Para minimizar os pedidos descartados, recomendamos que os servidores VPN estejam configurados com um intervalo de tempo de pelo menos 60 segundos. Se necessário, ou para reduzir os pedidos descartados nos registos do evento, pode aumentar o valor de tempo limite do servidor VPN para 90 ou 120 segundos.

Devido a este comportamento do protocolo UDP, o servidor NPS poderia receber um pedido duplicado e enviar outra solicitação de MFA, mesmo depois de o utilizador já ter respondido ao pedido inicial. Para evitar esta condição de tempo, a extensão NPS de autenticação multi-factor Azure continua a filtrar e a descartar pedidos duplicados até 10 segundos após o envio de uma resposta bem sucedida para o servidor VPN.

![Diagrama do servidor NPS continua a descartar pedidos duplicados do servidor VPN durante dez segundos após a reação de uma resposta bem sucedida](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

Mais uma vez, poderá ver pedidos descartados nos registos de eventos do servidor NPS, mesmo quando o pedido de autenticação multi-factor Azure foi bem sucedido. Este comportamento é esperado e não indica um problema com o servidor NPS ou a extensão NPS de autenticação multi-factor Azure.

## <a name="plan-your-deployment"></a>Planear a sua implementação

A extensão NPS lida automaticamente com a redundância, pelo que não precisa de uma configuração especial.

Pode criar os mais servidores NPS ativados por autenticação multi-factor Azure que precisar. Se instalar vários servidores, deverá utilizar um certificado de cliente de diferença para cada um deles. A criação de um certificado para cada servidor significa que pode atualizar cada cert individualmente e não se preocupar com o tempo de inatividade em todos os seus servidores.

Os servidores VPN encaminham pedidos de autenticação, pelo que precisam de estar atentos aos novos servidores NPS ativados por autenticação multi-factor Azure.

## <a name="prerequisites"></a>Pré-requisitos

A extensão NPS destina-se a trabalhar com a sua infraestrutura existente. Certifique-se de que tem os seguintes pré-requisitos antes de começar.

### <a name="licenses"></a>Licenças

A Extensão NPS para autenticação multi-factor Azure está disponível para clientes com [licenças para autenticação multi-factor Azure](multi-factor-authentication.md). As licenças baseadas no consumo para autenticação multi-factor Azure, como por utilizador ou por licença de autenticação, não são compatíveis com a extensão NPS.

### <a name="software"></a>Software

Windows Server 2012 ou superior.

### <a name="libraries"></a>Bibliotecas

Tem de instalar manualmente a seguinte biblioteca:

- [Visual C++ Redistributable para Visual Studio 2015](https://www.microsoft.com/download/details.aspx?id=48145)

As seguintes bibliotecas são instaladas automaticamente com a extensão.

- [Pacotes redistribuíveis Visual C++ para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Módulo de Diretório Ativo Microsoft Azure para windows PowerShell versão 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

O Módulo de Diretório Ativo microsoft Azure para Windows PowerShell também é instalado através de um script de configuração que executou como parte do processo de configuração, se ainda não estiver presente. Não há necessidade de instalar este módulo antes do tempo se ainda não estiver instalado.

### <a name="azure-active-directory"></a>Azure Active Directory

Todos os que usam a extensão NPS devem ser sincronizados com Azure AD usando Azure AD Connect, e devem ser registados para MFA.

Quando instalar a extensão, precisa da *identificação* do inquilino e credenciais de administração para o seu inquilino AD Azure. Para obter a iD do inquilino, complete os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global do inquilino Azure.
1. Procure e selecione o **Diretório Ativo Azure**.
1. Na página **geral,** é mostrada a informação do *Arrendatário.* Ao lado do ID do *inquilino,* selecione o ícone **Copy,** como mostra o seguinte exemplo de imagem:

   ![Obter a ID do Inquilino do portal Azure](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>Requisitos de rede

O servidor NPS deve ser capaz de comunicar com os seguintes URLs sobre as portas 80 e 443:

* *https: \/ /adnotifications.windowsazure.com*
* *https: \/ /login.microsoftonline.com*
* *https: \/ /credentials.azure.com*

Além disso, a conectividade com os urls seguintes é necessária para completar a [configuração do adaptador utilizando o script PowerShell fornecido](#run-the-powershell-script):

* *https: \/ /login.microsoftonline.com*
* *https: \/ /provisioningapi.microsoftonline.com*
* *https: \/ /aadcdn.msauth.net*

## <a name="prepare-your-environment"></a>Preparar o ambiente

Antes de instalar a extensão NPS, prepare-o para lidar com o tráfego de autenticação.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Ativar o papel de NPS num servidor ligado a domínios

O servidor NPS conecta-se ao AZure AD e autentica os pedidos de MFA. Escolha um servidor para este papel. Recomendamos a escolha de um servidor que não lide com pedidos de outros serviços, porque a extensão NPS lança erros para quaisquer pedidos que não sejam RADIUS. O servidor NPS deve ser configurado como o servidor de autenticação primária e secundária para o seu ambiente. Não pode pedir pedidos de RADIUS a outro servidor.

1. No seu servidor, abra **o Gestor do Servidor**. Selecione **Adicionar Funções e Funcionalidades Assistente** no menu *Quickstart.*
2. Para o seu tipo de instalação, escolha **instalação baseada em funções ou baseada em recursos.**
3. Selecione a função de servidor **de Política de Rede e Serviços de Acesso.** Uma janela pode aparecer para informá-lo sobre as funcionalidades adicionais necessárias para executar este papel.
4. Continue através do assistente até à página *de Confirmação.* Quando estiver pronto, **selecione Instalar**.

Pode levar alguns minutos para instalar a função do servidor NPS. Quando terminar, continue com as seguintes secções para configurar este servidor para lidar com os pedidos de RADIUS que chegam a partir da solução VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configure a sua solução VPN para comunicar com o servidor NPS

Dependendo da solução VPN que utiliza, os passos para configurar a sua política de autenticação RADIUS variam. Configure a sua política VPN para apontar para o seu servidor RADIUS NPS.

### <a name="sync-domain-users-to-the-cloud"></a>Sincronizar os utilizadores de domínio para a nuvem

Este passo pode já estar completo no seu inquilino, mas é bom verificar duas vezes que o Azure AD Connect sincronizou as suas bases de dados recentemente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Selecione **Azure Ative Directory**  >  **Azure AD Connect**
3. Verifique se o seu estado de sincronização está **ativado** e que a sua última sincronização foi há menos de uma hora.

Se precisar de iniciar uma nova ronda de sincronização, consulte [a sincronização do Azure AD Connect: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Determinar quais os métodos de autenticação que os seus utilizadores podem utilizar

Existem dois fatores que afetam os métodos de autenticação disponíveis com uma extensão NPS:

1. O algoritmo de encriptação de palavra-passe utilizado entre o cliente RADIUS (VPN, servidor Netscaler ou outro) e os servidores NPS.
   - **O PAP** suporta todos os métodos de autenticação do Azure Multi-Factor Authentication na nuvem: chamada telefónica, mensagem de texto unidirecionais, notificação de aplicações móveis, fichas de hardware OATH e código de verificação de aplicações móveis.
   - **CHAPV2** e **EAP** suportam chamada telefónica e notificação de aplicativos móveis.

      > [!NOTE]
      > Quando implementar a extensão NPS, utilize estes fatores para avaliar quais os métodos disponíveis para os seus utilizadores. Se o seu cliente RADIUS suporta PAP, mas o cliente UX não tem campos de entrada para um código de verificação, então a chamada telefónica e a notificação de aplicações móveis são as duas opções suportadas.
      >
      > Além disso, se o seu cliente VPN UX suportar campos de entrada e tiver configurado a Política de Acesso à Rede, a autenticação poderá ter sucesso. No entanto, nenhum dos atributos RADIUS configurados na Política de Rede não será aplicado nem ao Dispositivo de Acesso à Rede, como o servidor RRAS, nem ao cliente VPN. Como resultado, o cliente VPN pode ter mais acesso do que o desejado, ou menos sem acesso.

2. Os métodos de entrada que a aplicação do cliente (VPN, servidor Netscaler, ou outro) podem lidar. Por exemplo, o cliente VPN tem alguns meios para permitir que o utilizador escreva um código de verificação a partir de um texto ou aplicação móvel?

Pode [desativar métodos de autenticação não suportados](howto-mfa-mfasettings.md#verification-methods) em Azure.

### <a name="register-users-for-mfa"></a>Registar utilizadores de MFA

Antes de implementar e utilizar a extensão NPS, os utilizadores que são obrigados a realizar a autenticação multi-factor Azure precisam de ser registados para MFA. Para testar a extensão à medida que a implementa, também precisa de pelo menos uma conta de teste que esteja totalmente registada para autenticação multi-factor Azure.

Se precisar de criar e configurar uma conta de teste, utilize os seguintes passos:

1. Inscreva-se [https://aka.ms/mfasetup](https://aka.ms/mfasetup) com uma conta de teste.
2. Siga as instruções para configurar um método de verificação.
3. No portal Azure como utilizador administrativo, [crie uma política de Acesso Condicional](howto-mfa-getstarted.md#create-conditional-access-policy) para exigir a autenticação de vários fatores para a conta de teste.

> [!IMPORTANT]
>
> Certifique-se de que os utilizadores se registaram com sucesso para autenticação multi-factor Azure. Se os utilizadores tiverem registado anteriormente apenas para reposição de password de autosserviço (SSPR), *os Meditods StrongAuthentication* estão ativados para a sua conta. A autenticação multi-factor Azure é aplicada quando *a StrongAuthenticationMethods* está configurada, mesmo que o utilizador apenas se registe para SSPR.
>
> O registo combinado de segurança pode ser ativado que configura simultanemente a autenticação de SSPR e Azure Multi-Factor. Para obter mais informações, consulte [Ativar o registo combinado de informações de segurança no Diretório Ativo Azure](howto-registration-mfa-sspr-combined.md).
>
> Também pode [forçar os utilizadores a re-registar métodos de autenticação](howto-mfa-userdevicesettings.md#manage-user-authentication-options) se anteriormente apenas ativarem sSPR.

## <a name="install-the-nps-extension"></a>Instale a extensão NPS

> [!IMPORTANT]
> Instale a extensão NPS num servidor diferente do ponto de acesso VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Descarregue e instale a extensão NPS para Azure MFA

Para descarregar e instalar a extensão NPS, complete os seguintes passos:

1. [Descarregue a extensão NPS](https://aka.ms/npsmfa) a partir do Microsoft Download Center.
1. Copie o binário para o Servidor de Política de Rede que pretende configurar.
1. Executar *setup.exe* e siga as instruções de instalação. Se encontrar erros, certifique-se de que as [bibliotecas da secção pré-requisito](#libraries) foram instaladas com sucesso.

#### <a name="upgrade-the-nps-extension"></a>Atualizar a extensão NPS

Se posteriormente atualizar uma instalação de extensão NPS existente, para evitar o reinício do servidor subjacente, complete os seguintes passos:

1. Desinstale a versão existente.
1. Executar o novo instalador.
1. Reinicie o serviço *Desemis (IAS) do Servidor de Política de Rede (IAS).*

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell

O instalador cria um script PowerShell `C:\Program Files\Microsoft\AzureMfa\Config` (onde `C:\` está a sua unidade de instalação). Este script PowerShell executa as seguintes ações cada vez que é executado:

* Cria um certificado auto-assinado.
* Associa a chave pública do certificado ao principal de serviço na Azure AD.
* Armazena o certificado na loja de certificados de máquina local.
* Concede acesso à chave privada do certificado para o Utilizador da Rede.
* Reinicia o serviço NPS.

A menos que queira utilizar os seus próprios certificados (em vez dos certificados auto-assinados que o script PowerShell gera), execute o script PowerShell para completar a instalação de extensão NPS. Se instalar a extensão em vários servidores, cada servidor deverá ter o seu próprio certificado.

Para fornecer capacidades de equilíbrio de carga ou para redundância, repita estes passos em servidores NPS adicionais, conforme desejado:

1. Abra um pedido do Windows PowerShell como administrador.
1. Alterar diretórios para onde o instalador criou o script PowerShell:

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. Executar o script PowerShell criado pelo instalador.

   > [!IMPORTANT]
   > Para os clientes que usam o Governo Azure ou as nuvens Azure China 21Vianet, edite primeiro os `Connect-MsolService` cmdlets no *AzureMfaNpsExtnConfigSetup.ps1* script para incluir os parâmetros *AzureEnvironment* para a nuvem necessária. Por exemplo, *especificar -AzureEnvironment USGovernment* ou *-AzureEnvironment AzureChinaCloud*.
   >
   > Para obter mais informações, consulte [a referência do parâmetro Connect-MsolService](/powershell/module/msonline/connect-msolservice#parameters).

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. Quando solicitado, inscreva-se na Azure AD como administrador.
1. PowerShell pede a identificação do seu inquilino. Utilize o *ID* GUID do inquilino que copiou do portal Azure na secção pré-requisitos.
1. Uma mensagem de sucesso é mostrada quando o script está terminado.  

Se o seu certificado de computador anterior tiver expirado e tiver sido gerado um novo certificado, deverá eliminar quaisquer certificados caducados. Ter certificados caducados pode causar problemas com o início da Extensão NPS.

> [!NOTE]
> Se utilizar os seus próprios certificados em vez de gerar certificados com o script PowerShell, certifique-se de que se alinham com a convenção de nomeação NPS. O nome do assunto deve ser **CN= \<TenantID\> EXTENSÃO OU=Microsoft NPS**.

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Microsoft Azure Government ou Azure China 21Vianet passos adicionais

Para os clientes que utilizam o Governo Azure ou as nuvens Azure China 21Vianet, são necessários os seguintes passos de configuração adicionais em cada servidor NPS.

> [!IMPORTANT]
> Configurar apenas estas definições de registo se for um cliente Azure Government ou Azure China 21Vianet.

1. Se você é um cliente Azure Government ou Azure China 21Vianet, abra o **Editor de Registo** no servidor NPS.
1. Navegue para `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`.
1. Para os clientes do Governo Azure, desa estalem os seguintes valores-chave.:

    | Chave do registo       | Valor |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Para os clientes Azure China 21Vianet, desa um conjunto de valores-chave:

    | Chave do registo       | Valor |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. Repita os dois passos anteriores para definir os valores das chaves de registo para cada servidor NPS.
1. Reinicie o serviço NPS para cada servidor NPS.

    Para um impacto mínimo, retire cada servidor NPS da rotação NLB um de cada vez e aguarde que todas as ligações escorram.

### <a name="certificate-rollover"></a>Capotamento de certificado

Com o lançamento *1.0.1.32* da extensão NPS, a leitura de vários certificados é agora suportada. Esta capacidade ajuda a facilitar as atualizações dos certificados de rolamento antes da sua expiração. Se a sua organização estiver a executar uma versão anterior da extensão NPS, atualize para a versão *1.0.1.32* ou superior.

Os certificados criados pelo `AzureMfaNpsExtnConfigSetup.ps1` script são válidos por 2 anos. Monitorize certificados para expiração. Os certificados para a extensão NPS são colocados na loja de certificados *de Computador Local* sob o *Personal* e são *emitidos para* o ID do inquilino fornecido ao script de instalação.

Quando um certificado se aproxima da data de validade, deve ser criado um novo certificado para o substituir.  Este processo é realizado executando o `AzureMfaNpsExtnConfigSetup.ps1` novo e mantendo a mesma identificação do inquilino quando solicitado. Este processo deve ser repetido em cada servidor NPS no seu ambiente.

## <a name="configure-your-nps-extension"></a>Configure a sua extensão de NPS

Com o ambiente preparado e a extensão NPS agora instalada nos servidores necessários, pode configurar a extensão.

Esta secção inclui considerações de design e sugestões para implementações bem sucedidas de extensão de NPS.

### <a name="configuration-limitations"></a>Limitações de configuração

- A extensão NPS para autenticação multi-factor Azure não inclui ferramentas para migrar utilizadores e configurações do MFA Server para a nuvem. Por esta razão, sugerimos a utilização da extensão para novas implementações, em vez de implantação existente. Se utilizar a extensão numa implementação existente, os seus utilizadores têm de efetuar novamente a prova para preencher os seus dados de MFA na nuvem.  
- A extensão NPS utiliza a UPN a partir do ambiente AD DS no local para identificar o utilizador na Autenticação Multi-Factor Azure para a realização do Auth Secundário. A extensão pode ser configurada para usar um identificador diferente, como iD de login alternativo ou campo DS AD personalizado que não o UPN. Para obter mais informações, consulte o artigo, [opções de configuração avançadas para a extensão NPS para autenticação multi-factor.](howto-mfa-nps-extension-advanced.md)
- Nem todos os protocolos de encriptação suportam todos os métodos de verificação.
   - **O PAP** suporta chamadas telefónicas, mensagens de texto unidirecionais, notificação de aplicações móveis e código de verificação de aplicações móveis
   - **CHAPV2** e **EAP** suportam chamada telefónica e notificação de aplicativos móveis

### <a name="control-radius-clients-that-require-mfa"></a>Controle clientes RADIUS que requerem MFA

Uma vez que você ativa MFA para um cliente RADIUS usando a extensão NPS, todas as autenticações para este cliente são necessárias para realizar MFA. Se pretender ativar o MFA para alguns clientes RADIUS, mas não para outros, pode configurar dois servidores NPS e instalar a extensão em apenas um deles.

Configurar clientes RADIUS que pretende exigir que a MFA envie pedidos para o servidor NPS configurados com a extensão, e outros clientes RADIUS para o servidor NPS não configurados com a extensão.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Prepare-se para utilizadores que não estão inscritos para MFA

Se tiver utilizadores que não estão inscritos para MFA, pode determinar o que acontece quando tentam autenticar. Para controlar este comportamento, utilize a definição *REQUIRE_USER_MATCH* na trajetória de registo *HKLM\Software\Microsoft\AzureMFA*. Esta definição tem uma única opção de configuração:

| Chave | Valor | Predefinição |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | VERDADEIRO/FALSO | Não definido (equivalente a VERDADEIRO) |

Esta definição determina o que fazer quando um utilizador não está matriculado para MFA. Quando a tecla não existe, não está definida, ou está definida para *TRUE*, e o utilizador não está inscrito, a extensão falha o desafio MFA.

Quando a chave está definida para *FALSE* e o utilizador não está matriculado, a autenticação prossegue sem efetuar MFA. Se um utilizador estiver matriculado em MFA, deve autenticar-se com MFA mesmo *que REQUIRE_USER_MATCH* esteja definido como *FALSE*.

Pode optar por criar esta tecla e defini-la para *FALSE* enquanto os seus utilizadores estão a bordo, e pode ainda não estar inscrita para autenticação multi-factor Azure. No entanto, uma vez que a definição da chave permite que os utilizadores que não estão inscritos para o MFA se inscrevam, deve remover esta chave antes de ir para a produção.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="nps-extension-health-check-script"></a>Roteiro de verificação de saúde de extensão de NPS

O seguinte script está disponível para executar etapas básicas de verificação de saúde ao resolver problemas na extensão de NPS.

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Como posso verificar se o certificado do cliente está instalado como esperado?

Procure o certificado auto-assinado criado pelo instalador na loja cert, e verifique se a chave privada tem permissões concedidas ao *serviço de rede de utilizador*. O cert tem um nome de **assunto de \<tenantid\> CN, OU = Extensão NPS do Microsoft**

Os certificados auto-assinados gerados pelo `AzureMfaNpsExtnConfigSetup.ps1` script têm uma vida útil de validade de dois anos. Ao verificar se o certificado está instalado, deve também verificar se o certificado não expirou.

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>Como posso verificar se o certificado do meu cliente está associado ao meu inquilino em Azure AD?

Abra o comando PowerShell e execute os seguintes comandos:

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Estes comandos imprimem todos os certificados que associam o seu inquilino à sua instância da extensão NPS na sua sessão PowerShell. Procure o seu certificado exportando o seu certificado de cliente como um ficheiro *X.509 codificado base-64 (.cer)* sem a chave privada, e compare-o com a lista da PowerShell.

O seguinte comando criará um ficheiro denominado *npscertificado* na raiz do seu *C:* unidade em formato *.cer*.

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Depois de executar este comando, vá até à raiz do seu *C:* unidade, localize o ficheiro e clique duas vezes nele. Vá para os detalhes, e desça para "impressão digital". Compare a impressão digital do certificado instalado no servidor com este. As impressões digitais do certificado devem coincidir.

Os certificados de tempo *válidos* e *válidos,* que estão na forma legível pelo homem, podem ser utilizados para filtrar os desajustados óbvios se o comando devolver mais de um cert.

### <a name="why-cannot-i-sign-in"></a>Por que não posso me inscrever?

Verifique se a sua senha não expirou. A extensão NPS não suporta a alteração de palavras-passe como parte do fluxo de trabalho de entrada. Contacte o pessoal de TI da sua organização para mais assistência.

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Porque é que os meus pedidos estão a falhar com o erro da ADAL?

Este erro pode dever-se a uma de várias razões. Utilize os seguintes passos para resolver problemas:

1. Reinicie o servidor NPS.
2. Verifique se o certificado do cliente está instalado como esperado.
3. Verifique se o certificado está associado ao seu inquilino no Azure AD.
4. Certifique-se de que `https://login.microsoftonline.com/` está acessível a partir do servidor que executa a extensão.

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Porque é que a autenticação falha com um erro nos registos HTTP indicando que o utilizador não é encontrado?

Verifique se o AD Connect está em funcionamento e se o utilizador está presente tanto no ambiente AD DS no local como no Azure AD.

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Por que vejo HTTP ligar erros em registos com todas as minhas autenticações falhando?

Certifique-se de que https://adnotifications.windowsazure.com está acessível a partir do servidor que executa a extensão NPS.

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Porque é que a autenticação não está a funcionar, apesar da presença de um certificado válido?

Se o seu certificado de computador anterior tiver expirado e tiver sido gerado um novo certificado, elimine quaisquer certificados caducados. Os certificados expirados podem causar problemas com o início da extensão NPS.

Para verificar se tem um certificado válido, consulte a *Loja de Certificados da Conta Informática* local utilizando o MMC e certifique-se de que o certificado não passou a data de validade. Para gerar um certificado recém-válido, reexamque os passos do script do [instalador PowerShell](#run-the-powershell-script).

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>Por que vejo pedidos descartados nos registos do servidor NPS?

Um servidor VPN pode enviar pedidos repetidos para o servidor NPS se o valor de tempo limite for demasiado baixo. O servidor NPS deteta estes pedidos duplicados e deita-os fora. Este comportamento é por design e não indica um problema com o servidor NPS ou com a extensão NPS de autenticação multi-factor Azure.

Para obter mais informações sobre o porquê de ver pacotes descartados nos registos do servidor NPS, consulte o comportamento do [protocolo RADIUS e a extensão NPS](#radius-protocol-behavior-and-the-nps-extension) no início deste artigo.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Gerir os Protocolos TLS/SSL e Conjuntos de Cifras

Recomenda-se que as suites de cifra mais antigas e mais fracas sejam desativadas ou removidas, a menos que a sua organização o exija. Informações sobre como completar esta tarefa podem ser encontradas no artigo, [Protocolos SSL/TLS e Cipher Suites para FS AD](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Resolução adicional de problemas

Orientações adicionais de resolução de problemas e possíveis soluções podem ser encontradas no artigo, [Resolver mensagens de erro a partir da extensão NPS para autenticação multi-factor Azure](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral e configuração do Servidor de Política de Rede no Servidor do Windows](/windows-server/networking/technologies/nps/nps-top)

- Configure iDs alternativos para login ou crie uma lista de exceções para IPs que não deve realizar verificação em duas etapas em [opções de configuração avançada para a extensão NPS para autenticação multi-factor](howto-mfa-nps-extension-advanced.md)

- Saiba como integrar os [servidores Remote Desktop Gateway](howto-mfa-nps-extension-rdg.md) e [VPN](howto-mfa-nps-extension-vpn.md) utilizando a extensão NPS

- [Resolver mensagens de erro da extensão NPS para Multi-Factor Authentication do Azure](howto-mfa-nps-extension-errors.md)