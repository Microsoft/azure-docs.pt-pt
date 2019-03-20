---
title: O Azure Active Directory pass-through Authentication detalhada da segurança | Documentos da Microsoft
description: Este artigo descreve como autenticação de pass-through do Azure Active Directory (Azure AD) protege as contas no local
services: active-directory
keywords: Autenticação do Azure AD Connect pass-through, Active Directory, de instalação necessários componentes para o Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80b8db3bb2e7a21011508f30492bf99c7ecca583
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096865"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>O Azure Active Directory pass-through Authentication detalhada da segurança

Este artigo fornece uma descrição mais detalhada de como funciona a autenticação de pass-through do Azure Active Directory (Azure AD). Ele se concentra nos aspetos de segurança do recurso. Este artigo é para segurança e os administradores de TI, diretor conformidade e responsáveis pela segurança e as organizações de tamanho de outros profissionais de TI responsáveis pela segurança de TI e de conformidade em pequenas e médias ou grandes empresas.

Os tópicos abordados incluem:
- Obter informações técnicas detalhadas sobre como instalar e registar os agentes de autenticação.
- Obter informações técnicas detalhadas sobre a encriptação de palavras-passe durante o início de sessão do utilizador.
- A segurança dos canais entre locais agentes de autenticação e o Azure AD.
- Informações técnicas detalhadas sobre como manter os agentes de autenticação operacionalmente seguro.
- Outros tópicos relacionados à segurança.

## <a name="key-security-capabilities"></a>Capacidades de segurança de chaves

Estes são os aspectos de segurança chave desta funcionalidade:
- Baseia-se numa arquitetura de vários inquilinos segura que fornece o isolamento de pedidos de início de sessão entre inquilinos.
- Palavras-passe no local nunca são armazenadas na cloud de nenhuma forma.
- Agentes de autenticação no local que escutar e responder a pedidos de validação da palavra-passe fazer apenas ligações de saída a partir de dentro da sua rede. Não existe nenhum requisito para instalar estes agentes de autenticação numa rede de perímetro (DMZ). Como melhor prática, tratar todos os servidores com agentes de autenticação, como sistemas de 0 (consulte [referência](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Apenas portas não padrão (80 e 443) são utilizadas para comunicação de saída dos agentes de autenticação para o Azure AD. Não precisa de abrir portas na firewall. 
  - Porta 443 é utilizada para todas as comunicações de saída autenticada.
  - Porta 80 é utilizada apenas para baixar as listas de revogação de certificados (CRL) para garantir que nenhum dos certificados utilizados por esse recurso foram revogadas.
  - Para obter a lista completa dos requisitos de rede, consulte [autenticação de pass-through do Azure Active Directory: Guia de introdução](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- Palavras-passe que os utilizadores de fornecer durante o início de sessão são encriptadas na cloud antes dos agentes de autenticação no local aceitá-los para a validação relativamente ao Active Directory.
- O canal HTTPS entre o Azure AD e o agente de autenticação no local é protegido ao utilizar a autenticação mútua.
- Protege as contas de utilizador ao trabalhar de forma totalmente integrada com [políticas de acesso condicional do Azure AD](../active-directory-conditional-access-azure-portal.md), incluindo multi-factor Authentication (MFA), [antigos de autenticação de bloqueio](../conditional-access/conditions.md) e por [ filtrar os ataques de palavra-passe de força bruta](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Componentes envolvidos

Para obter detalhes gerais sobre segurança de dados e serviço operacional, do Azure AD, consulte a [Centro de fidedignidade](https://azure.microsoft.com/support/trust-center/). Os seguintes componentes envolvidos ao utilizar a autenticação pass-through para o início de sessão do utilizador:
- **STS do Azure AD**: Um sem monitoração de estado de segurança token (STS serviço) que processa pedidos de início de sessão e emite tokens de segurança para browsers dos utilizadores, os clientes ou serviços, conforme necessário.
- **O Azure Service Bus**: Fornece a capacidade de cloud a comunicação com mensagens empresariais e a comunicação de reencaminhamentos que lhe permite ligar soluções no local à cloud.
- **O agente de autenticação do Azure AD Connect**: Um componente no local que escuta e responde a pedidos de validação da palavra-passe.
- **Base de dados SQL do Azure**: Contém informações sobre agentes de autenticação do seu inquilino, incluindo as chaves de encriptação e de metadados.
- **Active Directory**: No local do Active Directory, onde são armazenadas as contas de utilizador e as palavras-passe.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalação e registo dos agentes de autenticação

Agentes de autenticação instalados e registrados com o Azure AD quando a:
   - [Ativar a autenticação pass-through através do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Adicionar mais agentes de autenticação para garantir a disponibilidade elevada de pedidos de início de sessão](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Obter um trabalho do agente de autenticação envolve três fases principais:

1. Instalação do agente de autenticação
2. Registo do agente de autenticação
3. Inicialização do agente de autenticação

As secções seguintes abordam estas fases em detalhes.

### <a name="authentication-agent-installation"></a>Instalação do agente de autenticação

Apenas os administradores globais podem instalar um agente de autenticação (através da utilização do Azure AD Connect ou autónoma) num servidor no local. Instalação adiciona duas novas entradas para o **painel de controlo** > **programas** > **programas e funcionalidades** lista:
- O agente de autenticação próprio aplicativo. Esta aplicação é executada com [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) privilégios.
- A aplicação de Atualizador que é utilizada para atualizar automaticamente o agente de autenticação. Esta aplicação é executada com [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) privilégios.

### <a name="authentication-agent-registration"></a>Registo do agente de autenticação

Depois de instalar o agente de autenticação, tem de registar-se com o Azure AD. O Azure AD atribui cada agente de autenticação de um certificado exclusivo, a identidade digital que pode ser usado para uma comunicação segura com o Azure AD.

O procedimento de registro também vincula o agente de autenticação com o seu inquilino. Isto garante que o AD do Azure saiba que este agente de autenticação específico é a única autorizada para processar pedidos de validação da palavra-passe para o seu inquilino. Este procedimento é repetido para cada novo agente de autenticação que se registrar.

Os agentes de autenticação utilize os seguintes passos para se registrar com o Azure AD:

![Registo do agente](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Azure AD pedidos pela primeira vez que um administrador global iniciar sessão com o Azure AD com as respetivas credenciais. Durante o início de sessão, o agente de autenticação adquire um token de acesso que pode ser usado em nome de administrador global.
2. O agente de autenticação, em seguida, gera um par de chaves: uma chave pública e uma chave privada.
    - O par de chaves é gerado através de encriptação padrão da RSA 2048 bits.
    - A chave privada permanece no servidor no local onde reside o agente de autenticação.
3. O agente de autenticação faz um pedido de "Registro" para o Azure AD através de HTTPS, com os seguintes componentes incluídos no pedido:
    - O token de acesso obtido no passo 1.
    - A chave pública gerada no passo 2.
    - Um certificado (CSR ou pedido de certificado) de solicitação de assinatura. Este pedido aplica-se de um certificado de identidade digital no Azure AD como sua autoridade de certificação (AC).
4. O Azure AD valida o token de acesso no pedido de registo e verifica que o pedido veio de um administrador global.
5. O Azure AD, em seguida, assina e envia um certificado de identidade digital para o agente de autenticação.
    - A AC de raiz no Azure AD é utilizado para assinar os certificados. 

      > [!NOTE]
      > É esta AC _não_ nas autoridades de certificação de raiz fidedigna Windows armazenar.
    - A AC é utilizada apenas pela funcionalidade de autenticação pass-through. A AC é utilizada apenas para assinar os CSRs durante o registo do agente de autenticação.
    -  Nenhum dos outros serviços do Azure AD Utilize esta AC.
    - Assunto do certificado (nome distinto ou DN) está definido como sua ID do inquilino. Esse DN é um GUID que identifica exclusivamente o seu inquilino. Esse DN examina o certificado para utilização apenas com o seu inquilino.
6. Azure AD armazena a chave pública do agente de autenticação numa base de dados SQL do Azure, que apenas do Azure AD tem acesso a.
7. O certificado (emitido no passo 5) é armazenado no servidor no local no arquivo de certificados do Windows (especificamente no [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) localização). Ele é usado, o agente de autenticação e os aplicativos do Atualizador.

### <a name="authentication-agent-initialization"></a>Inicialização do agente de autenticação

Quando o agente de autenticação é iniciado, pela primeira vez após o registo ou depois de um servidor reiniciar, ele precisa de uma forma para comunicar de forma segura com o serviço do Azure AD e comece a aceitar pedidos de validação da palavra-passe.

![Inicialização do agente](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Eis como agentes de autenticação são inicializados:

1. O agente de autenticação faz um pedido de arranque do sistema de saída para o Azure AD. 
    - Essa solicitação é feita através da porta 443 e é através de um canal HTTPS mutuamente autenticado. O pedido utiliza o mesmo certificado que foi emitido durante o registo do agente de autenticação.
2. O Azure AD responde à solicitação, fornecendo uma chave de acesso a uma fila do Service bus do Azure que seja exclusivo para o seu inquilino e que esteja identificado pelo seu ID de inquilino.
3. O agente de autenticação faz uma ligação HTTPS saída persistente (através da porta 443) para a fila. 
    - O agente de autenticação está agora pronto para recuperar e processar pedidos de validação da palavra-passe.

Se tiver vários agentes de autenticação registrado no seu inquilino, em seguida, o procedimento de inicialização garante que cada um deles estabelece uma ligação à mesma fila do Service Bus. 

## <a name="process-sign-in-requests"></a>Processar pedidos de início de sessão 

O diagrama seguinte mostra como a autenticação pass-through processa pedidos de início de sessão do utilizador.

![Início de sessão no processo](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Autenticação pass-through processa um pedido de início de sessão do utilizador da seguinte forma: 

1. Um utilizador tenta aceder a uma aplicação, por exemplo, [Outlook Web App](https://outlook.office365.com/owa).
2. Se o utilizador não está já iniciou sessão, a aplicação redireciona o navegador para a página de início de sessão do Azure AD.
3. Criar uma responde de serviço do Azure AD STS com o **sessão do utilizador** página.
4. O utilizador introduz o respetivo nome de utilizador para o **sessão do utilizador** página e, em seguida, seleciona a **próxima** botão.
5. O utilizador introduz a palavra-passe para o **sessão do utilizador** página e, em seguida, seleciona a **início de sessão** botão.
6. O nome de utilizador e palavra-passe são enviadas para o Azure AD STS de um pedido POST de HTTPS.
7. O Azure AD STS obtém as chaves públicas para todos os agentes de autenticação registrado no seu inquilino da base de dados SQL do Azure e encripta a palavra-passe ao utilizá-los. 
    - Produz valores de palavras-passe de "N" encriptado para os agentes de autenticação "N" registados no seu inquilino.
8. O Azure AD STS coloca o pedido de validação da palavra-passe, que consiste o nome de utilizador e os valores de palavra-passe encriptada, para a fila de barramento de serviço específico do seu inquilino.
9. Uma vez que os agentes de autenticação inicializado persistentemente estiver ligados à fila do Service Bus, um dos agentes de autenticação disponíveis obtém o pedido de validação da palavra-passe.
10. O agente de autenticação localiza o valor de palavra-passe encriptada que é específico para a sua chave pública, através de um identificador e desencripta a mesma, utilizando a respetiva chave privada.
11. O agente de autenticação tenta validar o nome de utilizador e a palavra-passe no Active Directory no local utilizando o [API do Win32 LogonUser](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) com o **dwLogonType** parâmetro definido como **LOGON32_LOGON_NETWORK**. 
    - Esta API é a mesma API que é utilizada pelos serviços de Federação do Active Directory (AD FS) para iniciar sessão dos utilizadores num cenário de início de sessão federado.
    - Essa API depende do processo de resolução padrão no Windows Server para localizar o controlador de domínio.
12. O agente de autenticação receber o resultado do Active Directory, como êxito, nome de utilizador ou palavra-passe incorreta ou a palavra-passe expirada.
13. O agente de autenticação reencaminha o resultado voltar ao STS do Azure AD através de um canal HTTPS mutuamente autenticado saído através da porta 443. A autenticação mútua utiliza o certificado emitido anteriormente para o agente de autenticação durante o registo.
14. O Azure AD STS verifica que o resultado está relacionada com o pedido específico início de sessão no seu inquilino.
15. O Azure AD STS continua com o procedimento de início de sessão, conforme configurado. Por exemplo, se a validação da palavra-passe foi concluída com êxito, o utilizador poderá ser submetido à multi-factor Authentication ou redirecionado para a aplicação.

## <a name="operational-security-of-the-authentication-agents"></a>Segurança operacional dos agentes de autenticação

Para garantir que a autenticação pass-through permanece operacionalmente segura, do Azure AD periodicamente renova certificados dos agentes de autenticação. O Azure AD aciona as renovações. As renovações não são regidas pelos agentes de autenticação propriamente ditas.

![Segurança operacional](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Para renovar a confiança de um agente de autenticação com o Azure AD:

1. O agente de autenticação periodicamente faz o ping do Azure AD intervalos de poucas horas para verificar se está na altura de renovar o seu certificado. O certificado é renovado 30 dias antes do vencimento.
    - Esta verificação é feita através de um canal HTTPS mutuamente autenticado e utiliza o mesmo certificado que foi emitido durante o registo.
2. Se o serviço indica que está na altura de renovar, o agente de autenticação gera um novo par de chaves: uma chave pública e uma chave privada.
    - Estas chaves são geradas através da encriptação de RSA 2048 bits padrão.
    - A chave privada nunca deixa o servidor no local.
3. O agente de autenticação, em seguida, faz um pedido de "renovação do certificado" para o Azure AD através de HTTPS, com os seguintes componentes incluídos no pedido:
    - O certificado existente que é obtido a partir da localização de CERT_SYSTEM_STORE_LOCAL_MACHINE no arquivo de certificados do Windows. Não existe nenhum administrador global envolvidas neste procedimento, portanto, não há nenhum token de acesso necessário em nome de administrador global.
    - A chave pública gerada no passo 2.
    - Um certificado (CSR ou pedido de certificado) de solicitação de assinatura. Este pedido se aplica a um novo certificado de identidade digital, com o Azure AD como sua autoridade de certificação.
4. O Azure AD valida o certificado existente no pedido de renovação de certificado. Em seguida, ele verifica que o pedido veio de um agente de autenticação registrado no seu inquilino.
5. Se o certificado existente ainda é válido, do Azure AD, em seguida, inicia um novo certificado de identidade digital e emite o novo certificado para o agente de autenticação. 
6. Se o certificado existente tiver expirado, o AD do Azure elimina o agente de autenticação da lista do seu inquilino de agentes de autenticação registados. Em seguida, um administrador global tem de instalar manualmente e registar um novo agente de autenticação.
    - Utilize a AC de raiz do Azure AD para assinar os certificados.
    - Defina o assunto do certificado (nome distinto ou DN) para o ID de inquilino, um GUID que identifica exclusivamente o seu inquilino. O DN examina o certificado com o seu inquilino apenas.
6. Azure AD armazena a nova chave pública do agente de autenticação numa base de dados SQL do Azure que apenas tem acesso a. Ele também invalida a antiga chave pública associada ao agente de autenticação.
7. O novo certificado (emitido no passo 5), em seguida, é armazenado no servidor no arquivo de certificados do Windows (especificamente no [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) localização).
    - Uma vez que o procedimento de renovação de confiança acontece de forma não interativa (sem a presença de administrador global), o agente de autenticação já não tem acesso para atualizar o certificado existente na localização CERT_SYSTEM_STORE_LOCAL_MACHINE. 
    
   > [!NOTE]
   > Este procedimento não remove o certificado propriamente dito da localização CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. O novo certificado é utilizado para autenticação a partir deste ponto em. Cada subsequente renovação do certificado substitui o certificado na localização CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Atualização automática dos agentes de autenticação

O aplicativo de Atualizador atualiza automaticamente o agente de autenticação quando for lançada uma nova versão. A aplicação não processar as solicitações de validação da palavra-passe para o seu inquilino. 

Azure AD aloja a nova versão do software como um assinado **pacote do Windows Installer (MSI)**. O MSI está assinado usando [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) com SHA256 como o algoritmo de texto implícita. 

![Atualização automática](./media/how-to-connect-pta-security-deep-dive/pta5.png)

A atualização automática de um agente de autenticação:

1. Os pings de aplicativo do Atualizador do Azure AD para verificar se existe uma nova versão do agente de autenticação disponível a cada hora. 
    - Essa verificação é feita através de um canal HTTPS mutuamente autenticado utilizando o mesmo certificado que foi emitido durante o registo. O agente de autenticação e o Atualizador partilham o certificado armazenado no servidor.
2. Se uma nova versão estiver disponível, o Azure AD devolve o MSI assinado novamente para o Atualizador.
3. O Atualizador verifica se o MSI está assinado pela Microsoft.
4. O Atualizador é executado o MSI. Esta ação envolve os seguintes passos:

   > [!NOTE]
   > O Atualizador é executado com [Sistema Local](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) privilégios.

    - Para o serviço de agente de autenticação
    - Instala a nova versão do agente de autenticação no servidor
    - Reinicia o serviço de agente de autenticação

>[!NOTE]
>Se tiver vários agentes de autenticação registrado no seu inquilino, o Azure AD não renovar os respetivos certificados ou atualizá-las ao mesmo tempo. Em vez disso, o Azure AD tão gradualmente assim faz para garantir a disponibilidade elevada de pedidos de início de sessão.
>


## <a name="next-steps"></a>Passos Seguintes
- [Limitações atuais](how-to-connect-pta-current-limitations.md): Saiba quais cenários são suportados e quais não são.
- [Guia de introdução](how-to-connect-pta-quick-start.md): Começar a trabalhar na autenticação pass-through do Azure AD.
- [Migrar do AD FS para autenticação pass-through](https://aka.ms/adfstoptadpdownload) -um guia detalhado para migrar do AD FS (ou outras tecnologias de Federação) para autenticação pass-through.
- [Bloqueio do smart](../authentication/howto-password-smart-lockout.md): Configure a capacidade de bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [Como funciona](how-to-connect-pta-how-it-works.md): Aprenda as noções básicas de como funciona a autenticação pass-through do Azure AD.
- [Perguntas mais frequentes sobre](how-to-connect-pta-faq.md): Encontre respostas para perguntas mais frequentes.
- [Resolver problemas de](tshoot-connect-pass-through-authentication.md): Saiba como resolver problemas comuns com a funcionalidade de autenticação pass-through.
- [SSO totalmente integrado do Azure AD](how-to-connect-sso.md): Saiba mais sobre esta funcionalidade complementar.
