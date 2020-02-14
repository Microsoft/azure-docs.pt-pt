---
title: Azure Ative Directory Pass-through Autenticação segurança profundo mergulho profundo Microsoft Docs
description: Este artigo descreve como o Azure Ative Directory (Azure AD) Pass-through Autenticação protege as suas contas no local
services: active-directory
keywords: Autenticação de passagem de ligação Azure AD, instala o Diretório Ativo, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ddce8d4d7ca1f03c0a57d0f0c8c41ac122973e0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185564"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Ative Directory Pass-through Autenticação segurança profunda

Este artigo fornece uma descrição mais detalhada de como funciona a Autenticação Pass-through do Azure Ative (Azure AD). Centra-se nos aspetos de segurança da funcionalidade. Este artigo destina-se a administradores de segurança e de TI, oficiais de conformidade e segurança, bem como outros profissionais de TI responsáveis pela segurança e conformidade das TI em organizações de pequena saúde ou grandes empresas.

Os tópicos abordados incluem:
- Informações técnicas detalhadas sobre como instalar e registar os Agentes de Autenticação.
- Informações técnicas detalhadas sobre a encriptação de senhas durante o registo do utilizador.
- A segurança dos canais entre agentes de autenticação no local e a AD Azure.
- Informações técnicas detalhadas sobre como manter os Agentes de Autenticação operacionais seguros.
- Outros tópicos relacionados com a segurança.

## <a name="key-security-capabilities"></a>Principais capacidades de segurança

Estes são os principais aspetos de segurança desta funcionalidade:
- É construído sobre uma arquitetura multiarrendatária segura que proporciona isolamento de pedidos de inscrição entre inquilinos.
- As palavras-passe no local nunca são armazenadas na nuvem de qualquer forma.
- No local, os agentes de autenticação que ouvem e respondem a pedidos de validação de passwords apenas fazem ligações de saída a partir da sua rede. Não existe qualquer obrigação de instalar estes Agentes de Autenticação numa rede de perímetro (DMZ). Como melhor prática, trate todos os servidores executando agentes de autenticação como sistemas de nível 0 (ver [referência](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Apenas as portas standard (80 e 443) são utilizadas para a comunicação de saída dos Agentes de Autenticação para a AD Azure. Não precisa abrir portas de entrada na sua firewall. 
  - A porta 443 é utilizada para todas as comunicações autenticadas de saída.
  - A porta 80 é utilizada apenas para descarregar as Listas de Revogação de Certificados (CRLs) para garantir que nenhum dos certificados utilizados por esta função tenha sido revogado.
  - Para obter a lista completa dos requisitos da rede, consulte [autenticação de passagem de diretório ativo Azure: Quickstart](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- As palavras-passe que os utilizadores fornecem durante o sessão são encriptadas na nuvem antes que os Agentes de Autenticação no local as aceitem para validação contra o Diretório Ativo.
- O canal HTTPS entre a AD Azure e o Agente de Autenticação no local é protegido através da autenticação mútua.
- Protege as suas contas de utilizador trabalhando perfeitamente com as políticas de [Acesso Condicional Azure AD,](../active-directory-conditional-access-azure-portal.md)incluindo a Autenticação Multi-Factor (MFA), [bloqueando a autenticação do legado](../conditional-access/concept-conditional-access-conditions.md) e [filtrando ataques](../authentication/howto-password-smart-lockout.md)de senha de força bruta .

## <a name="components-involved"></a>Componentes envolvidos

Para mais detalhes sobre a operacional, serviço e segurança de dados da Azure AD, consulte o [Trust Center](https://azure.microsoft.com/support/trust-center/). Os seguintes componentes estão envolvidos quando utilizar a Autenticação Pass-through para iniciar sessão do utilizador:
- **Azure AD STS**: Um serviço de token de segurança apátrida (STS) que processa pedidos de login e emite fichas de segurança aos navegadores, clientes ou serviços dos utilizadores, conforme necessário.
- **Azure Service Bus**: Fornece comunicação ativada pela nuvem com mensagens empresariais e transmite comunicação que o ajuda a ligar soluções no local com a nuvem.
- Agente de **autenticação Azure AD Connect**: Um componente no local que ouve e responde a pedidos de validação de passwords.
- **Base de Dados Azure SQL**: Contém informações sobre os Agentes de Autenticação do seu inquilino, incluindo os seus metadados e chaves de encriptação.
- **Diretório Ativo**: No local, diretório ativo, onde as suas contas de utilizador e as suas palavras-passe são armazenadas.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalação e registo dos Agentes de Autenticação

Os agentes de autenticação são instalados e registados na Azure AD quando o mesmo:
   - [Ativar a autenticação pass-through através do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Adicione mais Agentes de Autenticação para garantir a elevada disponibilidade de pedidos de sessão](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
O trabalho de um Agente de Autenticação envolve três fases principais:

1. Instalação do Agente de Autenticação
2. Registo do Agente de Autenticação
3. Inicialização do Agente de Autenticação

As seguintes secções discutem estas fases em detalhe.

### <a name="authentication-agent-installation"></a>Instalação do Agente de Autenticação

Apenas administradores globais podem instalar um Agente de Autenticação (utilizando o Azure AD Connect ou autónomo) num servidor no local. A instalação adiciona duas novas entradas à lista de **programas** > **do Painel de Controlo** > Programas e **Funcionalidades:**
- A aplicação do Agente de Autenticação em si. Esta aplicação funciona com privilégios [NetworkService.](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx)
- A aplicação Atualizador que é usada para atualizar automaticamente o Agente de Autenticação. Esta aplicação funciona com privilégios [Do Sistema Local.](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)

### <a name="authentication-agent-registration"></a>Registo do Agente de Autenticação

Depois de instalar o Agente de Autenticação, tem de se registar com a AD Azure. A Azure AD atribui a cada Agente de Autenticação um certificado de identidade digital único que pode usar para uma comunicação segura com a AD Azure.

O procedimento de registo também liga o Agente de Autenticação ao seu inquilino. Isto garante que a Azure AD sabe que este Agente de Autenticação específico é o único autorizado a lidar com pedidos de validação de passwords para o seu inquilino. Este procedimento é repetido para cada novo Agente de Autenticação que você registra.

Os Agentes de Autenticação utilizam os seguintes passos para se registarem na AD Azure:

![Registo de agente](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. A Azure AD pede primeiro que um administrador global assine a Azure AD com as suas credenciais. Durante o registo, o Agente de Autenticação adquire um sinal de acesso que pode utilizar em nome do administrador global.
2. O Agente de Autenticação gera então um par chave: uma chave pública e uma chave privada.
    - O par chave é gerado através da encriptação padrão RSA 2048 bit.
    - A chave privada permanece no servidor no local onde reside o Agente de Autenticação.
3. O Agente de Autenticação faz um pedido de "registo" à Azure AD em HTTPS, com os seguintes componentes incluídos no pedido:
    - O sinal de acesso adquirido no passo 1.
    - A chave pública gerada no passo 2.
    - Um Pedido de Assinatura de Certificado (CSR ou Pedido de Certificado). Este pedido aplica-se a um certificado de identidade digital, com a Azure AD como autoridade de certificados (CA).
4. A Azure AD valida o sinal de acesso no pedido de registo e verifica que o pedido veio de um administrador global.
5. A Azure AD assina e envia um certificado de identidade digital para o Agente de Autenticação.
    - A raiz ca em Azure AD é usada para assinar o certificado. 

      > [!NOTE]
      > Este CA _não_ está na loja windows trusted root authorities.
    - O CA é utilizado apenas pela função de Autenticação Pass-through. O CA é utilizado apenas para assinar CSRs durante o registo do Agente de Autenticação.
    -  Nenhum dos outros serviços da AD Azure usa este CA.
    - O sujeito do certificado (Nome Distinto ou DN) está definido para o seu ID de inquilino. Este DN é um GUID que identifica exclusivamente o seu inquilino. Este DN aplica o certificado para uso apenas com o seu inquilino.
6. A Azure AD armazena a chave pública do Agente de Autenticação numa base de dados Azure SQL, à qual apenas a Azure AD tem acesso.
7. O certificado (emitido no passo 5) é armazenado no servidor no local na loja de certificados Windows (especificamente na [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) localização). É utilizado tanto pelo Agente de Autenticação como pelas aplicações do Atualizador.

### <a name="authentication-agent-initialization"></a>Inicialização do Agente de Autenticação

Quando o Agente de Autenticação começa, quer pela primeira vez após o registo, quer após o reinício do servidor, precisa de uma forma de comunicar de forma segura com o serviço Azure AD e começar a aceitar pedidos de validação de passwords.

![Inicialização do agente](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Eis como os agentes de autenticação são inicializados:

1. O Agente de Autenticação faz um pedido de saque de saída para a AD Azure. 
    - Este pedido é feito sobre a porta 443 e é sobre um canal HTTPS mutuamente autenticado. O pedido utiliza o mesmo certificado emitido durante o registo do Agente de Autenticação.
2. A Azure AD responde ao pedido fornecendo uma chave de acesso a uma fila de ônibus de serviço Azure que é única para o seu inquilino e que é identificada pelo seu id inquilino.
3. O Agente de Autenticação faz uma ligação HTTPS de saída persistente (sobre a porta 443) à fila. 
    - O Agente de Autenticação está agora pronto para recuperar e lidar com pedidos de validação de passwords.

Se tiver vários Agentes de Autenticação registados no seu inquilino, o procedimento de inicialização garante que cada um se liga à mesma fila de ônibus de serviço. 

## <a name="process-sign-in-requests"></a>Processar pedidos de inscrição 

O diagrama seguinte mostra como a autenticação pass-through processa os pedidos de inscrição do utilizador.

![Iniciar o sessão](./media/how-to-connect-pta-security-deep-dive/pta3.png)

A Autenticação Pass-through trata de um pedido de inscrição do utilizador da seguinte forma: 

1. Um utilizador tenta aceder a uma aplicação, por exemplo, [aplicação web do Outlook.](https://outlook.office365.com/owa)
2. Caso o utilizador ainda não esteja inscrito, a aplicação redireciona o navegador para a página de entrada de ada d.I. Azure.
3. O serviço Azure AD STS responde com a página **de entrada do Utilizador.**
4. O utilizador introduz o seu nome de utilizador na página **de entrada** do Utilizador e, em seguida, seleciona o botão **Seguinte.**
5. O utilizador introduz a sua palavra-passe na página **de entrada** do Utilizador e, em seguida, seleciona o botão **'Iniciar sessão'.**
6. O nome de utilizador e a palavra-passe são submetidos a STS AD Azure num pedido HTTPS POST.
7. A Azure AD STS recupera chaves públicas para todos os Agentes de Autenticação registados no seu inquilino a partir da base de dados Azure SQL e encripta a palavra-passe utilizando-as.
    - Produz valores de senha encriptados "N" para agentes de autenticação "N" registados no seu inquilino.
8. A Azure AD STS coloca o pedido de validação de palavras-passe, que consiste no nome de utilizador e nos valores de palavra-passe encriptados, na fila do Ônibus de serviço específica para o seu inquilino.
9. Como os Agentes de Autenticação inicializados estão persistentemente ligados à fila do Autocarro de Serviço, um dos Agentes de Autenticação disponíveis recupera o pedido de validação da palavra-passe.
10. O Agente de Autenticação localiza o valor de palavra-passe encriptado específico da sua chave pública, utilizando um identificador, e desencripta-o utilizando a sua chave privada.
11. O Agente de Autenticação tenta validar o nome de utilizador e a palavra-passe contra o Diretório Ativo no local utilizando a [API Win32 LogonUser](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) com o parâmetro **dwLogonType** definido para **LOGON32_LOGON_NETWORK**. 
    - Esta API é a mesma API que é usada pelos Serviços da Federação de Diretórios Ativos (AD FS) para assinar os utilizadores num cenário de inscrição federada.
    - Esta API conta com o processo de resolução padrão no Windows Server para localizar o controlador de domínio.
12. O Agente de Autenticação recebe o resultado do Diretório Ativo, como o sucesso, o nome de utilizador ou a palavra-passe incorretamente, ou a palavra-passe expirada.

   > [!NOTE]
   > Se o Agente de Autenticação falhar durante o processo de entrada, todo o pedido de entrada é retirado. Não existe a entrega de pedidos de inscrição de um agente de autenticação para outro Agente de Autenticação no local. Estes agentes só comunicam com a nuvem, e não uns com os outros.
   
13. O Agente de Autenticação reencaminha o resultado de volta para AD STS Azure sobre um canal HTTPS de saída mutuamente autenticado através da porta 443. A autenticação mútua utiliza o certificado anteriormente emitido ao Agente de Autenticação durante o registo.
14. A Azure AD STS verifica que este resultado está relacionado com o pedido específico de inscrição no seu inquilino.
15. A Azure AD STS continua com o procedimento de entrada tal como configurado. Por exemplo, se a validação da palavra-passe tiver sido bem sucedida, o utilizador poderá ser desafiado para autenticação multi-factor ou redirecionado de volta para a aplicação.

## <a name="operational-security-of-the-authentication-agents"></a>Segurança operacional dos Agentes de Autenticação

Para garantir que a autenticação pass-through permanece operacionalmente segura, a Azure AD renova periodicamente os certificados dos Agentes de Autenticação. A AD Azure despoleta as renovações. As renovações não são regidas pelos próprios Agentes de Autenticação.

![Segurança operacional](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Para renovar a confiança de um agente de autenticação com a Azure AD:

1. O Agente de Autenticação periodicamente pings Azure AD a cada poucas horas para verificar se é hora de renovar o seu certificado. O certificado é renovado 30 dias antes da sua expiração.
    - Este cheque é feito através de um canal HTTPS mutuamente autenticado e utiliza o mesmo certificado emitido durante o registo.
2. Se o serviço indicar que está na hora de renovar, o Agente de Autenticação gera um novo par de chaves: uma chave pública e uma chave privada.
    - Estas teclas são geradas através da encriptação padrão RSA 2048 bit.
    - A chave privada nunca sai do servidor no local.
3. O Agente de Autenticação efectoe então um pedido de "renovação de certificado" à Azure AD em HTTPS, com os seguintes componentes incluídos no pedido:
    - O certificado existente que é recuperado da CERT_SYSTEM_STORE_LOCAL_MACHINE localização na loja de certificados Windows. Não existe um administrador global envolvido neste processo, pelo que não é necessário um sinal de acesso em nome do administrador global.
    - A chave pública gerada no passo 2.
    - Um Pedido de Assinatura de Certificado (CSR ou Pedido de Certificado). Este pedido aplica-se a um novo certificado de identidade digital, com a Azure AD como autoridade de certificados.
4. A Azure AD valida o certificado existente no pedido de renovação do certificado. Depois verifica-se que o pedido veio de um Agente de Autenticação registado no seu inquilino.
5. Se o certificado existente ainda for válido, a Azure AD assina então um novo certificado de identidade digital e emite o novo certificado de volta ao Agente de Autenticação. 
6. Se o certificado existente tiver expirado, a Azure AD elimina o Agente de Autenticação da lista de Agentes de Autenticação registados do seu inquilino. Em seguida, um administrador global precisa de instalar e registar manualmente um novo Agente de Autenticação.
    - Utilize a raiz Azure AD CA para assinar o certificado.
    - Detete o assunto do certificado (Nome Distinto ou DN) para o seu ID de inquilino, um GUID que identifica exclusivamente o seu inquilino. O DN aplica a certidão apenas ao seu inquilino.
6. A Azure AD armazena a nova chave pública do Agente de Autenticação numa base de dados Azure SQL a que só tem acesso. Também invalida a antiga chave pública associada ao Agente de Autenticação.
7. O novo certificado (emitido no passo 5) é então armazenado no servidor na loja de certificados Windows (especificamente na [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) localização).
    - Como o procedimento de renovação de confiança acontece de forma não interativa (sem a presença do administrador global), o Agente de Autenticação já não tem acesso para atualizar o certificado existente na CERT_SYSTEM_STORE_LOCAL_MACHINE local. 
    
   > [!NOTE]
   > Este procedimento não retira o certificado do local CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. O novo certificado é utilizado para autenticação a partir deste ponto. Cada renovação subsequente do certificado substitui o certificado no local CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Atualização automática dos Agentes de Autenticação

A aplicação Atualização automática do Agente de Autenticação quando é lançada uma nova versão (com correções de erros ou melhorias de desempenho). A aplicação Updater não lida com quaisquer pedidos de validação de palavras-passe para o seu inquilino.

A Azure AD acolhe a nova versão do software como um pacote de **instalador windows (MSI)** assinado. O MSI é assinado usando o [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) com SHA256 como algoritmo de digestão. 

![Atualização automática](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Para atualizar automaticamente um Agente de Autenticação:

1. O Ad da aplicação Updater pings Azure AD a cada hora para verificar se existe uma nova versão do Agente de Autenticação disponível. 
    - Este cheque é feito através de um canal HTTPS mutuamente autenticado utilizando o mesmo certificado emitido durante o registo. O Agente de Autenticação e o Atualizador partilham o certificado armazenado no servidor.
2. Se uma nova versão estiver disponível, a Azure AD devolve o MSI assinado ao Updater.
3. O Updater verifica que o MSI é assinado pela Microsoft.
4. O Atualizador executa o MSI. Esta ação envolve os seguintes passos:

   > [!NOTE]
   > O Atualizador funciona com privilégios do [Sistema Local.](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)

    - Interrompe o serviço do Agente de Autenticação
    - Instala a nova versão do Agente de Autenticação no servidor
    - Reinicia o serviço do Agente de Autenticação

>[!NOTE]
>Se tiver vários Agentes de Autenticação registados no seu inquilino, a Azure AD não renova os seus certificados nem os atualiza ao mesmo tempo. Em vez disso, a Azure AD fá-lo um de cada vez para garantir a elevada disponibilidade de pedidos de entrada.
>


## <a name="next-steps"></a>Passos seguintes
- [Limitações atuais](how-to-connect-pta-current-limitations.md): Saiba quais os cenários suportados e quais não são.
- [Quickstart](how-to-connect-pta-quick-start.md): Levantar-se e correr na Autenticação de Passagem De AD Azure.
- [Migrar de AD FS para a Autenticação Pass-through](https://aka.ms/adfstoptadpdownload) - Um guia detalhado para migrar de AD FS (ou outras tecnologias da federação) para a Autenticação Pass-through.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Configure a capacidade de bloqueio inteligente no seu inquilino para proteger as contas do utilizador.
- [Como funciona:](how-to-connect-pta-how-it-works.md)Saiba o básico de como funciona a Autenticação Pass-through Azure AD.
- [Perguntas frequentes](how-to-connect-pta-faq.md): Encontre respostas para perguntas frequentes.
- [Resolução de problemas](tshoot-connect-pass-through-authentication.md): Aprenda a resolver problemas comuns com a função de Autenticação Pass-through.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Saiba mais sobre esta funcionalidade complementar.
