---
title: Fornecer capacidades Azure MFA usando NPS - Azure Ative Directory
description: Adicione capacidades de verificação em duas etapas baseadas em nuvem à sua infraestrutura de autenticação existente
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: f5c93e35b2a9124ac6d480b3719608ee3b4484a5
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84554833"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrar a infraestrutura NPS existente com o Multi-Factor Authentication do Azure

A extensão do Network Policy Server (NPS) para Azure MFA adiciona capacidades MFA baseadas na nuvem à sua infraestrutura de autenticação utilizando os servidores existentes. Com a extensão NPS, pode adicionar chamadas telefónicas, mensagens de texto ou verificação de aplicativos telefónicos ao fluxo de autenticação existente sem ter de instalar, configurar e manter novos servidores. 

Esta extensão foi criada para organizações que pretendem proteger as ligações VPN sem implantar o Azure MFA Server. A extensão NPS funciona como um adaptador entre o RADIUS e o Azure MFA baseado na nuvem para fornecer um segundo fator de autenticação para utilizadores federados ou sincronizados.

Ao utilizar a extensão NPS para Azure MFA, o fluxo de autenticação inclui os seguintes componentes: 

1. **O SERVIDOR NAS/VPN** recebe pedidos de clientes VPN e converte-os em pedidos RADIUS para servidores NPS. 
2. **O NPS Server** liga-se ao Ative Directory para realizar a autenticação primária para os pedidos RADIUS e, após o sucesso, passa o pedido a quaisquer extensões instaladas.  
3. **A extensão NPS** desencadeia um pedido ao Azure MFA para a autenticação secundária. Uma vez que a extensão recebe a resposta, e se o desafio MFA for bem sucedido, completa o pedido de autenticação fornecendo ao servidor NPS fichas de segurança que incluem uma reclamação de MFA, emitida pela Azure STS.  
4. **O Azure MFA** comunica com o Azure Ative Directory para recuperar os dados do utilizador e efetua a autenticação secundária utilizando um método de verificação configurado para o utilizador.

O diagrama que se segue ilustra este fluxo de pedido de autenticação de alto nível: 

![Diagrama de fluxo de autenticação](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planear a sua implementação

A extensão NPS lida automaticamente com a redundância, pelo que não precisa de uma configuração especial.

Pode criar os servidores NPS ativados por Azure MFA que precisar. Se instalar vários servidores, deverá utilizar um certificado de cliente de diferença para cada um deles. Criar um certificado para cada servidor significa que pode atualizar cada cert individualmente, e não se preocupar com o tempo de inatividade em todos os seus servidores.

Os servidores VPN encaminham pedidos de autenticação, por isso precisam de estar cientes dos novos servidores NPS ativados por Azure MFA.

## <a name="prerequisites"></a>Pré-requisitos

A extensão NPS destina-se a trabalhar com a sua infraestrutura existente. Certifique-se de que tem os seguintes pré-requisitos antes de começar.

### <a name="licenses"></a>Licenças

A Extensão NPS para Azure MFA está disponível para clientes com [licenças para autenticação multi-factor Azure](multi-factor-authentication.md) (incluída com Azure AD Premium, EMS ou uma licença autónoma MFA). As licenças baseadas no consumo para MFA Azure, como por utilizador ou por licença de autenticação, não são compatíveis com a extensão NPS. 

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 ou superior.

### <a name="libraries"></a>Bibliotecas

Estas bibliotecas são instaladas automaticamente com a extensão.

- [Pacotes redistribuíveis Visual C++ para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Módulo de Diretório Ativo Microsoft Azure para windows PowerShell versão 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

O Módulo de Diretório Ativo Microsoft Azure para Windows PowerShell está instalado, se ainda não estiver presente, através de um script de configuração que executou como parte do processo de configuração. Não é necessário instalar este módulo com antecedência se ainda não estiver instalado.

Tem de instalar manualmente a seguinte biblioteca:

- [Visual C++ Redistributable para Visual Studio 2015](https://www.microsoft.com/download/details.aspx?id=48145)

### <a name="azure-active-directory"></a>Azure Active Directory

Todos os que usam a extensão NPS devem ser sincronizados com o Azure Ative Directory usando o Azure AD Connect, e devem estar registados para MFA.

Quando instalar a extensão, precisa do diretório e credenciais de administração para o seu inquilino AD Azure. Pode encontrar o seu iD de diretório no [portal Azure](https://portal.azure.com). Inscreva-se como administrador. Procure e selecione o **Diretório Ativo Azure**e, em seguida, selecione **Propriedades**. Copie o GUID na caixa **de identificação** do diretório e guarde-o. Você usa este GUID como iD do inquilino quando instala a extensão NPS.

![Encontre o seu ID do Diretório sob propriedades do Azure Ative Directory](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>Requisitos de rede

O servidor NPS precisa de ser capaz de comunicar com os urls seguintes sobre as portas 80 e 443.

- https: \/ /adnotifications.windowsazure.com
- https:\//login.microsoftonline.com
- https: \/ /credentials.azure.com

Além disso, a conectividade com os urLs seguintes é necessária para completar a [configuração do adaptador utilizando o script PowerShell fornecido](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https: \/ /provisioningapi.microsoftonline.com
- https: \/ /aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Preparar o ambiente

Antes de instalar a extensão NPS, pretende preparar-lhe o ambiente para lidar com o tráfego de autenticação.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Ativar o papel de NPS num servidor ligado a domínios

O servidor NPS conecta-se ao Azure Ative Directory e autentica os pedidos de MFA. Escolha um servidor para este papel. Recomendamos a escolha de um servidor que não lide com pedidos de outros serviços, porque a extensão NPS lança erros para quaisquer pedidos que não sejam RADIUS. O servidor NPS deve ser configurado como o servidor de autenticação primária e secundária para o seu ambiente; não pode proxy RADIUS solicita a outro servidor.

1. No seu servidor, abra o **Assistente de Funções e Funcionalidades adicionais** a partir do menu Quickstart do Gestor do Servidor.
2. Escolha **instalação baseada em funções ou baseada em recursos** para o seu tipo de instalação.
3. Selecione a função de servidor **de Política de Rede e Serviços de Acesso.** Pode surgir uma janela para informá-lo das funcionalidades necessárias para executar este papel.
4. Continue através do assistente até à página de Confirmação. Selecione **Instalar**.

Agora que tem um servidor designado para NPS, também deve configurar este servidor para lidar com pedidos de RADIUS de entrada a partir da solução VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configure a sua solução VPN para comunicar com o servidor NPS

Dependendo da solução VPN que utiliza, os passos para configurar a sua política de autenticação RADIUS variam. Configure esta política para apontar para o seu servidor NPS RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Sincronizar os utilizadores de domínio para a nuvem

Este passo pode já estar completo no seu inquilino, mas é bom verificar duas vezes que o Azure AD Connect sincronizou as suas bases de dados recentemente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Selecione **Azure Ative Directory**  >  **Azure AD Connect**
3. Verifique se o seu estado de sincronização está **ativado** e que a sua última sincronização foi há menos de uma hora.

Se precisar de iniciar uma nova ronda de sincronização, nós as instruções em [Azure AD Connect Sync: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Determinar quais os métodos de autenticação que os seus utilizadores podem utilizar

Existem dois fatores que afetam os métodos de autenticação disponíveis com uma extensão NPS:

1. O algoritmo de encriptação de palavra-passe utilizado entre o cliente RADIUS (VPN, servidor Netscaler ou outro) e os servidores NPS.
   - **O PAP** suporta todos os métodos de autenticação do Azure MFA na nuvem: chamada telefónica, mensagem de texto unidirecionais, notificação de aplicações móveis, fichas de hardware OATH e código de verificação de aplicações móveis.
   - **CHAPV2** e **EAP** suportam chamada telefónica e notificação de aplicativos móveis.

      > [!NOTE]
      > Quando implementar a extensão NPS, utilize estes fatores para avaliar quais os métodos disponíveis para os seus utilizadores. Se o seu cliente RADIUS suporta PAP, mas o cliente UX não tem campos de entrada para um código de verificação, então a chamada telefónica e a notificação de aplicações móveis são as duas opções suportadas.
      >
      > Além disso, se o seu cliente VPN UX suportar o campo de entrada e tiver a Política de Acesso à Rede configurada - a autenticação poderá ter sucesso, no entanto nenhum dos atributos RADIUS configurados na Política de Rede não será aplicado nem ao Dispositivo de Acesso à Rede, como o servidor RRAS, nem ao cliente VPN. Como resultado, o cliente VPN pode ter mais acesso do que o desejado ou menos sem acesso.
      >

2. Os métodos de entrada que a aplicação do cliente (VPN, servidor Netscaler, ou outro) podem lidar. Por exemplo, o cliente VPN tem alguns meios para permitir que o utilizador escreva um código de verificação a partir de um texto ou aplicação móvel?

Pode [desativar métodos de autenticação não suportados](howto-mfa-mfasettings.md#verification-methods) em Azure.

### <a name="register-users-for-mfa"></a>Registar utilizadores de MFA

Antes de implementar e utilizar a extensão NPS, os utilizadores que são obrigados a realizar a verificação em duas etapas precisam de ser registados para MFA. Mais imediatamente, para testar a extensão à medida que a implementa, precisa de pelo menos uma conta de teste que esteja totalmente registada para a Autenticação Multi-Factor.

Use estes passos para iniciar uma conta de teste:

1. Inscreva-se [https://aka.ms/mfasetup](https://aka.ms/mfasetup) com uma conta de teste.
2. Siga as instruções para configurar um método de verificação.
3. [Crie uma política de Acesso Condicional](howto-mfa-getstarted.md#create-conditional-access-policy) para exigir a autenticação de vários fatores para a conta de teste.

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

1. [Descarregue a extensão NPS](https://aka.ms/npsmfa) a partir do Microsoft Download Center.
2. Copie o binário para o Servidor de Política de Rede que pretende configurar.
3. Executar *setup.exe* e seguir as instruções de instalação. Se encontrar erros, verifique duas vezes se as duas bibliotecas da secção pré-requisito foram instaladas com sucesso.

#### <a name="upgrade-the-nps-extension"></a>Atualizar a extensão NPS

Ao atualizar uma instalação de extensão NPS existente, para evitar o reinício do servidor subjacente, complete os seguintes passos:

1. Desinstale a versão existente
1. Executar o novo instalador
1. Reiniciar o serviço Dese-lhe (IAS) do Servidor de Política de Rede (IAS)

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell

O instalador cria um script PowerShell neste local: `C:\Program Files\Microsoft\AzureMfa\Config` (onde C:\ é a sua unidade de instalação). Este script PowerShell executa as seguintes ações cada vez que é executado:

- Crie um certificado auto-assinado.
- Associe a chave pública do certificado ao diretor de serviço na Azure AD.
- Guarde o certificado na loja local de cert.
- Conceder acesso à chave privada do certificado para o Utilizador da Rede.
- Reinicie o NPS.

A menos que queira utilizar os seus próprios certificados (em vez dos certificados auto-assinados que o script PowerShell gera), execute o PowerShell Script para completar a instalação. Se instalar a extensão em vários servidores, cada um deve ter o seu próprio certificado.

1. Executar o Windows PowerShell como administrador.
2. Mudar diretórios.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Executar o script PowerShell criado pelo instalador.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Inscreva-se no Azure AD como administrador.
5. PowerShell pede a identificação do seu inquilino. Utilize o Diretório ID GUID que copiou do portal Azure na secção pré-requisitos.
6. PowerShell mostra uma mensagem de sucesso quando o script está terminado.  

Repita estes passos em quaisquer servidores NPS adicionais que pretenda configurar para equilibrar a carga.

Se o seu certificado de computador anterior tiver expirado e tiver sido gerado um novo certificado, deverá eliminar quaisquer certificados caducados. Ter certificados caducados pode causar problemas com o início da Extensão NPS.

> [!NOTE]
> Se utilizar os seus próprios certificados em vez de gerar certificados com o script PowerShell, certifique-se de que se alinham com a convenção de nomeação NPS. O nome do assunto deve ser **CN= \<TenantID\> EXTENSÃO OU=Microsoft NPS**. 

### <a name="microsoft-azure-government-additional-steps"></a>Medidas adicionais do Governo microsoft Azure

Para os clientes que usam a nuvem do Governo Azure, são necessários os seguintes passos de configuração adicionais em cada servidor NPS.

> [!IMPORTANT]
> Só configura estas definições de registo se for cliente do Governo Azure.

1. Se é cliente do Governo Azure, abra o **Editor de Registos** no servidor NPS.
1. Navegue para `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`. Desaguise os seguintes valores-chave:

    | Chave do registo       | Valor |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Repita os dois passos anteriores para definir os valores das chaves de registo para cada servidor NPS.
1. Reinicie o serviço NPS para cada servidor NPS.

    Para um impacto mínimo, retire cada servidor NPS da rotação NLB um de cada vez e aguarde que todas as ligações escorram.

### <a name="certificate-rollover"></a>Capotamento de certificado

Com o lançamento 1.0.1.32 da extensão NPS, a leitura de vários certificados é agora suportada. Esta capacidade ajudará a facilitar as atualizações dos certificados de rolamento antes da sua expiração. Se a sua organização estiver a executar uma versão anterior da extensão NPS, deverá atualizar para a versão 1.0.1.32 ou superior.

Os certificados criados pelo `AzureMfaNpsExtnConfigSetup.ps1` script são válidos por 2 anos. As organizações de TI devem monitorizar os certificados de validade. Os certificados para a extensão NPS são colocados na loja de certificados de Computador Local sob o Personal e são emitidos para o ID do inquilino fornecido ao script.

Quando um certificado se aproxima da data de validade, deve ser criado um novo certificado para o substituir.  Este processo é realizado executando o `AzureMfaNpsExtnConfigSetup.ps1` novo e mantendo a mesma identificação do inquilino quando solicitado. Este processo deve ser repetido em cada servidor NPS no seu ambiente.

## <a name="configure-your-nps-extension"></a>Configure a sua extensão de NPS

Esta secção inclui considerações de design e sugestões para implementações bem sucedidas de extensão de NPS.

### <a name="configuration-limitations"></a>Limitações de configuração

- A extensão NPS para Azure MFA não inclui ferramentas para migrar utilizadores e configurações do MFA Server para a nuvem. Por esta razão, sugerimos a utilização da extensão para novas implementações, em vez de implantação existente. Se utilizar a extensão numa implementação existente, os seus utilizadores têm de efetuar novamente a prova para preencher os seus dados de MFA na nuvem.  
- A extensão NPS utiliza a UPN a partir do diretório ativo no local para identificar o utilizador no Azure MFA para a realização do Auth Secundário. A extensão pode ser configurada para usar um identificador diferente, como iD de login alternativo ou campo de diretório ativo personalizado que não o UPN. Para obter mais informações, consulte o artigo, [opções de configuração avançadas para a extensão NPS para autenticação multi-factor.](howto-mfa-nps-extension-advanced.md)
- Nem todos os protocolos de encriptação suportam todos os métodos de verificação.
   - **O PAP** suporta chamadas telefónicas, mensagens de texto unidirecionais, notificação de aplicações móveis e código de verificação de aplicações móveis
   - **CHAPV2** e **EAP** suportam chamada telefónica e notificação de aplicativos móveis

### <a name="control-radius-clients-that-require-mfa"></a>Controle clientes RADIUS que requerem MFA

Uma vez ativado o MFA para um cliente RADIUS utilizando a Extensão NPS, todas as autenticações para este cliente são necessárias para executar MFA. Se pretender ativar o MFA para alguns clientes RADIUS, mas não para outros, pode configurar dois servidores NPS e instalar a extensão em apenas um deles. Configurar clientes RADIUS que pretende exigir que a MFA envie pedidos para o servidor NPS configurados com a extensão, e outros clientes RADIUS para o servidor NPS não configurados com a extensão.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Prepare-se para utilizadores que não estão inscritos para MFA

Se tiver utilizadores que não estão inscritos para MFA, pode determinar o que acontece quando tentam autenticar. Utilize a definição de registo *REQUIRE_USER_MATCH* na trajetória de registo *HKLM\Software\Microsoft\AzureMFA* para controlar o comportamento da funcionalidade. Esta definição tem uma única opção de configuração:

| Chave | Valor | Predefinição |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | VERDADEIRO/FALSO | Não definido (equivalente a VERDADEIRO) |

O objetivo desta definição é determinar o que fazer quando um utilizador não está inscrito para MFA. Quando a tecla não existe, não está definida, ou está definida para TRUE, e o utilizador não está matriculado, então a extensão falha o desafio MFA. Quando a tecla está definida para FALSE e o utilizador não está matriculado, a autenticação prossegue sem efetuar MFA. Se um utilizador estiver matriculado em MFA, deve autenticar-se com MFA mesmo que REQUIRE_USER_MATCH esteja definido como FALSO.

Pode optar por criar esta chave e defini-la para FALSE enquanto os seus utilizadores estão a bordo, e pode ainda não estar toda inscrita para o Azure MFA. No entanto, uma vez que a definição da chave permite que os utilizadores que não estão inscritos para o MFA se inscrevam, deve remover esta chave antes de ir para a produção.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="nps-extension-health-check-script"></a>Roteiro de verificação de saúde de extensão de NPS

O seguinte script está disponível para executar etapas básicas de verificação de saúde ao resolver problemas na extensão de NPS.

[MFA_NPS_Troubleshooter.ps1](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Como posso verificar se o certificado do cliente está instalado como esperado?

Procure o certificado auto-assinado criado pelo instalador na loja cert, e verifique se a chave privada tem permissões concedidas ao **serviço de rede de utilizador**. O cert tem um nome de **assunto de \<tenantid\> CN, OU = Extensão NPS do Microsoft**

Os certificados auto-assinados gerados pelo script *AzureMfaNpsExtnConfigSetup.ps1* também têm uma vida útil de dois anos. Ao verificar se o certificado está instalado, deve também verificar se o certificado não expirou.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Como posso verificar se o meu cliente cert está associado ao meu inquilino em Azure Ative Directory?

Abra o comando PowerShell e execute os seguintes comandos:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Estes comandos imprimem todos os certificados que associam o seu inquilino à sua instância da extensão NPS na sua sessão PowerShell. Procure o seu certificado exportando o seu certificado de cliente como um ficheiro "Base-64 codificado X.509 (.cer)" sem a chave privada, e compare-o com a lista da PowerShell.

O seguinte comando criará um ficheiro denominado "npscertificate" na sua unidade "C:" em formato .cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Assim que executar este comando, vá à sua unidade C, localize o ficheiro e clique duas vezes nele. Vá aos detalhes e desloque-se para "impressão digital", compare a impressão digital do certificado instalado no servidor com este. As impressões digitais do certificado devem coincidir.

Os certificados de tempo válidos e válidos, que estão na forma legível pelo homem, podem ser utilizados para filtrar os desajustados óbvios se o comando devolver mais de um cert.

---

### <a name="why-cannot-i-sign-in"></a>Por que não posso me inscrever?

Verifique se a sua senha não expirou. A Extensão NPS não suporta a alteração de palavras-passe como parte do fluxo de trabalho de entrada. Contacte o pessoal de TI da sua organização para mais assistência.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Porque é que os meus pedidos estão a falhar com o erro da ADAL?

Este erro pode dever-se a uma de várias razões. Use estes passos para ajudar a resolver problemas:

1. Reinicie o servidor NPS.
2. Verifique se o certificado do cliente está instalado como esperado.
3. Verifique se o certificado está associado ao seu inquilino no Azure AD.
4. Certifique-se de que `https://login.microsoftonline.com/` está acessível a partir do servidor que executa a extensão.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Porque é que a autenticação falha com um erro nos registos HTTP indicando que o utilizador não é encontrado?

Verifique se o AD Connect está em execução e que o utilizador está presente tanto no Windows Ative Directory como no Azure Ative Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Por que vejo HTTP ligar erros em registos com todas as minhas autenticações falhando?

Certifique-se de que https://adnotifications.windowsazure.com está acessível a partir do servidor que executa a extensão NPS.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Porque é que a autenticação não está a funcionar, apesar da presença de um certificado válido?

Se o seu certificado de computador anterior tiver expirado e tiver sido gerado um novo certificado, deverá eliminar quaisquer certificados caducados. Ter certificados caducados pode causar problemas com o início da Extensão NPS.

Para verificar se tem um certificado válido, consulte a Loja de Certificados da Conta Informática local utilizando o MMC e certifique-se de que o certificado não passou a data de validade. Para gerar um certificado recém-válido, reexamque os passos sob a secção "[Executar o script PowerShell](#run-the-powershell-script)"

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Gerir os Protocolos TLS/SSL e Conjuntos de Cifras

Recomenda-se que as suites de cifra mais antigas e mais fracas sejam desativadas ou removidas, a menos que a sua organização o exija. Pode encontrar informações sobre como concluir esta tarefa no artigo [Gerir Protocolos SSL/TLS e Conjuntos de Cifras para o AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Resolução adicional de problemas

Orientações adicionais de resolução de problemas e possíveis soluções podem ser encontradas no artigo [Resolver mensagens de erro da extensão NPS para autenticação multi-factor Azure](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral e configuração do Servidor de Política de Rede no Servidor do Windows](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)

- Configure iDs alternativos para login ou crie uma lista de exceções para IPs que não deve realizar verificação em duas etapas em [opções de configuração avançada para a extensão NPS para autenticação multi-factor](howto-mfa-nps-extension-advanced.md)

- Saiba como integrar os [servidores Remote Desktop Gateway](howto-mfa-nps-extension-rdg.md) e [VPN](howto-mfa-nps-extension-vpn.md) utilizando a extensão NPS

- [Resolver mensagens de erro da extensão NPS para Multi-Factor Authentication do Azure](howto-mfa-nps-extension-errors.md)
