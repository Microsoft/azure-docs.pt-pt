---
title: Azure Ative Directory Pass-through Authentication security deep mergulho| Microsoft Docs
description: Este artigo descreve como o Azure Ative Directory (Azure AD) A Authentication Pass-through protege as suas contas no local
services: active-directory
keywords: Autenticação pass-through Azure AD Connect, instalar Diretório Ativo, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a73c2b1be4b17136ba19e7efb71c2b21359fdf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89280150"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Ative Directory Pass-through Authentication security deep mergulho

Este artigo fornece uma descrição mais detalhada de como funciona o Azure Ative Directory (Azure AD) A Authentication Pass-through. Centra-se nos aspetos de segurança da funcionalidade. Este artigo destina-se a administradores de segurança e TI, oficiais de conformidade e segurança, e outros profissionais de TI responsáveis pela segurança de TI e cumprimento em organizações de pequenas a médias empresas ou grandes empresas.

Os tópicos abordados incluem:
- Informação técnica detalhada sobre como instalar e registar os Agentes de Autenticação.
- Informações técnicas detalhadas sobre a encriptação de palavras-passe durante o sessão de acesso ao utilizador.
- A segurança dos canais entre os agentes de autenticação no local e a Azure AD.
- Informações técnicas detalhadas sobre como manter os Agentes de Autenticação operacionalmente seguros.
- Outros tópicos relacionados com a segurança.

## <a name="key-security-capabilities"></a>Principais capacidades de segurança

Estes são os principais aspetos de segurança desta funcionalidade:
- É construído sobre uma arquitetura segura multi-inquilinos que proporciona isolamento de pedidos de inscrição entre inquilinos.
- As palavras-passe no local nunca são armazenadas na nuvem sob qualquer forma.
- No local Agentes de autenticação que ouvem e respondem a pedidos de validação de passwords apenas fazem ligações de saída a partir da sua rede. Não existe qualquer obrigação de instalação destes Agentes de Autenticação numa rede de perímetro (DMZ). Como melhores práticas, trate todos os servidores que executam agentes de autenticação como sistemas tier 0 (ver [referência).](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)
- Apenas as portas standard (80 e 443) são utilizadas para comunicação de saída dos Agentes de Autenticação para Azure AD. Não precisa abrir portas de entrada na sua firewall. 
  - A porta 443 é utilizada para todas as comunicações de saída autenticadas.
  - A Porta 80 é utilizada apenas para o download das Listas de Revogação de Certificados (CRLs) para garantir que nenhum dos certificados utilizados por esta funcionalidade foi revogado.
  - Para obter a lista completa dos requisitos da rede, consulte [a autenticação pass-through do Diretório Ativo Azure: Quickstart](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- As palavras-passe que os utilizadores fornecem durante o sent-in são encriptadas na nuvem antes que os Agentes de Autenticação no local as aceitem para validação contra o Ative Directory.
- O canal HTTPS entre a Azure AD e o Agente de Autenticação no local é protegido através da autenticação mútua.
- Protege as suas contas de utilizador trabalhando perfeitamente com [as políticas de acesso condicional Azure AD](../conditional-access/overview.md), incluindo a Autenticação Multi-Factor (MFA), [bloqueando a autenticação do legado](../conditional-access/concept-conditional-access-conditions.md) e [filtrando ataques de senha de força bruta](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Componentes envolvidos

Para mais detalhes gerais sobre a operacional, serviço e segurança de dados da Azure AD, consulte o [Trust Center](https://azure.microsoft.com/support/trust-center/). Os seguintes componentes estão envolvidos quando utiliza a autenticação pass-through para o sent-in do utilizador:
- **Azure AD STS**: Um serviço de ficha de segurança apátrida (STS) que processa pedidos de login e emite fichas de segurança para navegadores, clientes ou serviços dos utilizadores, conforme necessário.
- **Azure Service Bus**: Fornece comunicação ativada em nuvem com mensagens empresariais e transmite comunicação que o ajuda a conectar soluções no local com a nuvem.
- **Azure AD Connect Authentication Agent**: Um componente no local que ouve e responde a pedidos de validação de palavras-passe.
- **Base de Dados Azure SQL**: Contém informações sobre os Agentes de Autenticação do seu inquilino, incluindo os seus metadados e chaves de encriptação.
- **Diretório Ativo**: Diretório Ativo no local, onde as suas contas de utilizador e as suas palavras-passe estão armazenadas.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalação e registo dos Agentes de Autenticação

Os Agentes de Autenticação são instalados e registados no Azure AD quando:
   - [Ativar a autenticação pass-through através do Azure AD Connect](./how-to-connect-pta-quick-start.md#step-2-enable-the-feature)
   - [Adicione mais Agentes de Autenticação para garantir a elevada disponibilidade de pedidos de inscrição](./how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) 
   
O funcionamento de um Agente de Autenticação envolve três fases principais:

1. Instalação do Agente de Autenticação
2. Registo do Agente de Autenticação
3. Inicialização do Agente de Autenticação

As seguintes secções discutem estas fases em detalhe.

### <a name="authentication-agent-installation"></a>Instalação do Agente de Autenticação

Apenas os administradores globais podem instalar um Agente de Autenticação (utilizando o Azure AD Connect ou autónomo) num servidor no local. A instalação adiciona duas novas entradas à lista de Programas e Funcionalidades do **Painel de**  >    >   Controlo:
- A própria aplicação do Agente de Autenticação. Esta aplicação é executado com privilégios [NetworkService.](/windows/win32/services/networkservice-account)
- A aplicação Updater que é usada para atualizar automaticamente o Agente de Autenticação. Esta aplicação é de 5000 em privilégios [do Sistema Local.](/windows/win32/services/localsystem-account)

>[!IMPORTANT]
>Do ponto de vista da segurança, os administradores devem tratar o servidor que executa o agente PTA como se fosse um controlador de domínio.  Os servidores de agentes PTA devem ser endurecidos ao longo das mesmas linhas que os descritos na [garantia de controladores de domínio contra ataque](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack)

### <a name="authentication-agent-registration"></a>Registo do Agente de Autenticação

Depois de instalar o Agente de Autenticação, tem de se registar com a Azure AD. A Azure AD atribui a cada Agente de Autenticação um certificado único de identidade digital que pode usar para comunicação segura com a Azure AD.

O procedimento de registo também liga o Agente de Autenticação ao seu inquilino. Isto garante que a Azure AD sabe que este Agente de Autenticação específico é o único autorizado a lidar com pedidos de validação de senhas para o seu inquilino. Este procedimento é repetido para cada novo Agente de Autenticação que registar.

Os Agentes de Autenticação utilizam as seguintes etapas para se registarem com a Azure AD:

![Registo de agente](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. A Azure AD solicita pela primeira vez que um administrador global inscreva-se na Azure AD com as suas credenciais. Durante a entrada, o Agente de Autenticação adquire um token de acesso que pode utilizar em nome do administrador global.
2. O Agente de Autenticação gera então um par de chaves: uma chave pública e uma chave privada.
    - O par de chaves é gerado através da encriptação padrão RSA 2048-bit.
    - A chave privada permanece no servidor no local onde reside o Agente de Autenticação.
3. O Agente de Autenticação faz um pedido de "registo" à Azure AD sobre HTTPS, com os seguintes componentes incluídos no pedido:
    - O símbolo de acesso adquirido no passo 1.
    - A chave pública gerada no passo 2.
    - Um Pedido de Assinatura de Certificado (CSR ou Pedido de Certificado). Este pedido aplica-se a um certificado de identidade digital, com a Azure AD como autoridade de certificados (CA).
4. A Azure AD valida o token de acesso no pedido de registo e verifica que o pedido veio de um administrador global.
5. A Azure AD assina e envia um certificado de identidade digital de volta para o Agente de Autenticação.
    - A raiz ca em Azure AD é usada para assinar o certificado. 

      > [!NOTE]
      > Esta AC _não_ está na loja do Windows Trusted Root Certificate Authorities.
    - O CA é utilizado apenas pela função de autenticação pass-through. A AC é utilizada apenas para assinar CSRs durante o registo do Agente de Autenticação.
    -  Nenhum dos outros serviços AD da Azure utiliza este CA.
    - O assunto do certificado (Nome Distinto ou DN) está definido para o seu ID do seu inquilino. Este DN é um GUID que identifica exclusivamente o seu inquilino. Este DN aplica o certificado de utilização apenas com o seu inquilino.
6. A Azure AD armazena a chave pública do Agente de Autenticação numa base de dados na Base de Dados Azure SQL, à qual apenas a Azure AD tem acesso.
7. O certificado (emitido no passo 5) é armazenado no servidor no local na loja de certificados do Windows (especificamente no [local CERT_SYSTEM_STORE_LOCAL_MACHINE).](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_LOCAL_MACHINE) É utilizado tanto pelo Agente de Autenticação como pelas aplicações do Updater.

### <a name="authentication-agent-initialization"></a>Inicialização do Agente de Autenticação

Quando o Agente de Autenticação começa, quer pela primeira vez após o registo, quer após o reinício do servidor, necessita de uma forma de comunicar de forma segura com o serviço AZure AD e começar a aceitar pedidos de validação de palavras-passe.

![Inicialização do agente](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Eis como os Agentes de Autenticação são inicializados:

1. O Agente de Autenticação faz um pedido de passe para a Azure AD. 
    - Este pedido é feito sobre a porta 443 e encontra-se sobre um canal HTTPS mutuamente autenticado. O pedido utiliza o mesmo certificado emitido durante o registo do Agente de Autenticação.
2. A Azure AD responde ao pedido fornecendo uma chave de acesso a uma fila de autocarros da Azure Service que é única para o seu inquilino e que é identificada pelo seu ID do seu inquilino.
3. O Agente de Autenticação faz uma ligação HTTPS de saída persistente (sobre a porta 443) para a fila. 
    - O Agente de Autenticação está agora pronto para recuperar e lidar com pedidos de validação de palavras-passe.

Se tiver vários Agentes de Autenticação registados no seu inquilino, então o procedimento de inicialização garante que cada um se conecta à mesma fila de Autocarros de Serviço. 

## <a name="process-sign-in-requests"></a>Pedidos de inscrição do processo 

O diagrama seguinte mostra como a autenticação pass-through processa os pedidos de inscrição do utilizador.

![Iniciar s-in](./media/how-to-connect-pta-security-deep-dive/pta3.png)

A autenticação pass-through trata de um pedido de inscrição do utilizador da seguinte forma: 

1. Um utilizador tenta aceder a uma aplicação, por exemplo, [a Outlook Web App](https://outlook.office365.com/owa).
2. Se o utilizador ainda não estiver assinado, a aplicação redireciona o navegador para a página de súmis AZure AD.
3. O serviço Azure AD STS responde com a página **de sinstrução do Utilizador.**
4. O utilizador introduz o seu nome de utilizador na página **de entrada do Utilizador** e, em seguida, seleciona o botão **Seguinte.**
5. O utilizador introduz a sua **palavra-passe na página de insusição do Utilizador** e, em seguida, seleciona o botão de iniciar **s-in.**
6. O nome de utilizador e a palavra-passe são submetidos a STS AZure AD num pedido HTTPS POST.
7. O Azure AD STS recupera chaves públicas para todos os Agentes de Autenticação registados no seu inquilino a partir da Base de Dados Azure SQL e encripta a palavra-passe utilizando-as.
    - Produz valores de palavra-passe encriptados "N" para agentes de autenticação "N" registados no seu inquilino.
8. O Azure AD STS coloca o pedido de validação de senha, que consiste no nome de utilizador e nos valores de senha encriptados, na fila do Service Bus específica para o seu inquilino.
9. Como os Agentes de Autenticação inicializados estão persistentemente ligados à fila do Service Bus, um dos Agentes de Autenticação disponível recupera o pedido de validação de senha.
10. O Agente de Autenticação localiza o valor da palavra-passe encriptada que é específico da sua chave pública, utilizando um identificador, e desencripta-o utilizando a sua chave privada.
11. O Agente de Autenticação tenta validar o nome de utilizador e a palavra-passe contra o Ative Directory no local, utilizando a [API do LogonUser Win32](/windows/win32/api/winbase/nf-winbase-logonusera) com o parâmetro **dwLogonType** definido para **LOGON32_LOGON_NETWORK**. 
    - Esta API é a mesma API que é utilizada pelos Ative Directory Federation Services (AD FS) para assinar nos utilizadores num cenário de inscrição federada.
    - Esta API baseia-se no processo de resolução padrão no Windows Server para localizar o controlador de domínio.
12. O Agente de Autenticação recebe o resultado do Ative Directory, como o sucesso, o nome de utilizador ou a palavra-passe incorreto, ou a palavra-passe expirada.

   > [!NOTE]
   > Se o Agente de Autenticação falhar durante o processo de inscrição, todo o pedido de inscrição será retirado. Não há entrega de pedidos de inscrição de um Agente de Autenticação para outro Agente de Autenticação no local. Estes agentes só comunicam com a nuvem, e não uns com os outros.
   
13. O Agente de Autenticação remete o resultado para Azure AD STS sobre um canal HTTPS mutuamente autenticado sobre a porta 443. A autenticação mútua utiliza o certificado previamente emitido ao Agente de Autenticação durante o registo.
14. A Azure AD STS verifica que este resultado está relacionado com o pedido específico de inscrição no seu inquilino.
15. O Azure AD STS continua com o procedimento de inscrição configurado. Por exemplo, se a validação da palavra-passe tiver sido bem sucedida, o utilizador poderá ser desafiado para autenticação multi-factor ou redirecionado de volta para a aplicação.

## <a name="operational-security-of-the-authentication-agents"></a>Segurança operacional dos Agentes de Autenticação

Para garantir que a autenticação pass-through permanece operacionalmente segura, a Azure AD renova periodicamente os certificados dos Agentes de Autenticação. A Azure AD desencadeia as renovações. As renovações não são regidas pelos próprios Agentes de Autenticação.

![Segurança operacional](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Para renovar a confiança de um agente de autenticação com a Azure AD:

1. O Agente de Autenticação pinga periodicamente a Azure AD de algumas em poucas horas para verificar se é hora de renovar o seu certificado. O certificado é renovado 30 dias antes do seu termo.
    - Esta verificação é feita através de um canal HTTPS mutuamente autenticado e utiliza o mesmo certificado que foi emitido durante o registo.
2. Se o serviço indicar que é hora de renovar, o Agente de Autenticação gera um novo par de chaves: uma chave pública e uma chave privada.
    - Estas chaves são geradas através da encriptação padrão RSA 2048-bit.
    - A chave privada nunca sai do servidor no local.
3. O Agente de Autenticação então faz um pedido de "renovação de certificado" à Azure AD sobre HTTPS, com os seguintes componentes incluídos no pedido:
    - O certificado existente que foi recuperado da localização CERT_SYSTEM_STORE_LOCAL_MACHINE na loja de certificados Windows. Não existe nenhum administrador global envolvido neste procedimento, pelo que não é necessário qualquer sinal de acesso em nome do administrador global.
    - A chave pública gerada no passo 2.
    - Um Pedido de Assinatura de Certificado (CSR ou Pedido de Certificado). Este pedido aplica-se a um novo certificado de identidade digital, com a Azure AD como autoridade de certificados.
4. A Azure AD valida o certificado existente no pedido de renovação do certificado. Verifica-se então que o pedido veio de um Agente de Autenticação registado no seu inquilino.
5. Se o certificado existente ainda for válido, a Azure AD assina então um novo certificado de identidade digital e emite o novo certificado de volta ao Agente de Autenticação. 
6. Se o certificado existente tiver expirado, a Azure AD elimina o Agente de Autenticação da lista de agentes de autenticação registados do seu inquilino. Em seguida, um administrador global precisa de instalar e registar manualmente um novo Agente de Autenticação.
    - Utilize a raiz AD AD para assinar o certificado.
    - Desaprova o assunto do certificado (Nome Distinto ou DN) para o seu ID do seu inquilino, um GUID que identifica exclusivamente o seu inquilino. O DN aplica o certificado apenas ao seu inquilino.
6. A Azure AD armazena a nova chave pública do Agente de Autenticação numa base de dados na Base de Dados Azure SQL a que só tem acesso. Também invalida a antiga chave pública associada ao Agente de Autenticação.
7. O novo certificado (emitido no passo 5) é então armazenado no servidor da loja de certificados Windows (especificamente na [localização CERT_SYSTEM_STORE_CURRENT_USER).](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_CURRENT_USER)
    - Como o procedimento de renovação de confiança acontece não interativamente (sem a presença do administrador global), o Agente de Autenticação já não tem acesso para atualizar o certificado existente no local CERT_SYSTEM_STORE_LOCAL_MACHINE. 
    
   > [!NOTE]
   > Este procedimento não retira o certificado em si do local CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. O novo certificado é utilizado para autenticação a partir deste ponto. Cada renovação subsequente do certificado substitui o certificado no local CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Atualização automática dos Agentes de Autenticação

A aplicação Updater atualiza automaticamente o Agente de Autenticação quando é lançada uma nova versão (com correções de erros ou melhorias de desempenho). A aplicação Updater não trata de nenhum pedido de validação de senha para o seu inquilino.

O Azure AD acolhe a nova versão do software como um pacote de instalador do **Windows (MSI)** assinado. O MSI é assinado usando [o Microsoft Authenticode](/previous-versions/windows/internet-explorer/ie-developer/platform-apis/ms537359(v=vs.85)) com SHA256 como algoritmo de digestão. 

![Atualização automática](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Para atualizar automaticamente um Agente de Autenticação:

1. A aplicação Updater pings Azure AD a cada hora para verificar se existe uma nova versão do Agente de Autenticação disponível. 
    - Esta verificação é feita através de um canal HTTPS mutuamente autenticado utilizando o mesmo certificado emitido durante o registo. O Agente de Autenticação e o Atualizador partilham o certificado armazenado no servidor.
2. Se uma nova versão estiver disponível, o Azure AD devolve o MSI assinado ao Updater.
3. O Updater verifica que o MSI é assinado pela Microsoft.
4. O Atualizador executa o MSI. Esta ação envolve os seguintes passos:

   > [!NOTE]
   > O Updater funciona com privilégios [do Sistema Local.](/windows/win32/services/localsystem-account)

    - Para o serviço de Agente de Autenticação
    - Instala a nova versão do Agente de Autenticação no servidor
    - Reinicia o serviço agente de autenticação

>[!NOTE]
>Se tiver vários Agentes de Autenticação registados no seu inquilino, a Azure AD não renova os seus certificados nem os atualiza ao mesmo tempo. Em vez disso, a Azure AD fá-lo um de cada vez para garantir a elevada disponibilidade de pedidos de inscrição.
>


## <a name="next-steps"></a>Passos seguintes
- [Limitações atuais](how-to-connect-pta-current-limitations.md): Saiba quais os cenários que são apoiados e quais não são.
- [Quickstart](how-to-connect-pta-quick-start.md): Levante-se e corra na Autenticação AD AD Azure.
- [Migrar de AD FS para Autenticação Pass-through](https://aka.ms/adfstoptadpdownload) - Um guia detalhado para migrar de FS AD (ou outras tecnologias da federação) para a Autenticação Pass-through.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Configure a capacidade de bloqueio inteligente no seu inquilino para proteger as contas do utilizador.
- [Como funciona](how-to-connect-pta-how-it-works.md): Saiba o básico de como funciona a autenticação pass-through Azure.
- [Perguntas frequentes](how-to-connect-pta-faq.md): Encontre respostas a perguntas frequentes.
- [Resolução de problemas](tshoot-connect-pass-through-authentication.md): Aprenda a resolver problemas comuns com a função De autenticação Pass-through.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Saiba mais sobre esta característica complementar.