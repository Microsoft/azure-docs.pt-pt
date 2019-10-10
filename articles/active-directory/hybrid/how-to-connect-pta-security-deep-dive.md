---
title: Aprofundamento da segurança de autenticação de passagem Azure Active Directory | Microsoft Docs
description: Este artigo descreve como a autenticação de passagem do Azure Active Directory (Azure AD) protege suas contas locais
services: active-directory
keywords: Azure AD Connect autenticação de passagem, instalar Active Directory, componentes necessários para o Azure AD, SSO, logon único
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
ms.openlocfilehash: d4f9686be08de2589cddadf741dadf243d0e7895
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174444"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Aprofundamento da segurança de autenticação de passagem Azure Active Directory

Este artigo fornece uma descrição mais detalhada de como funciona a autenticação de passagem do Azure Active Directory (Azure AD). Ele se concentra nos aspectos de segurança do recurso. Este artigo é para administradores de segurança e ti, diretor de conformidade e segurança e outros profissionais de ti responsáveis pela segurança e conformidade de ti em organizações de pequeno e médio porte ou grandes empresas.

Os tópicos abordados incluem:
- Informações técnicas detalhadas sobre como instalar e registrar os agentes de autenticação.
- Informações técnicas detalhadas sobre a criptografia de senhas durante a entrada do usuário.
- A segurança dos canais entre os agentes de autenticação local e o Azure AD.
- Informações técnicas detalhadas sobre como manter os agentes de autenticação em operação de forma segura.
- Outros tópicos relacionados à segurança.

## <a name="key-security-capabilities"></a>Principais recursos de segurança

Estes são os principais aspectos de segurança desse recurso:
- Ele foi criado em uma arquitetura segura de vários locatários que fornece isolamento de solicitações de entrada entre locatários.
- As senhas locais nunca são armazenadas na nuvem em nenhum formato.
- Os agentes de autenticação locais que escutam e respondem a solicitações de validação de senha apenas fazem conexões de saída de dentro de sua rede. Não há nenhum requisito para instalar esses agentes de autenticação em uma rede de perímetro (DMZ). Como prática recomendada, trate todos os servidores que executam agentes de autenticação como sistemas de camada 0 (consulte a [referência](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Somente as portas padrão (80 e 443) são usadas para a comunicação de saída dos agentes de autenticação para o Azure AD. Você não precisa abrir portas de entrada no firewall. 
  - A porta 443 é usada para todas as comunicações de saída autenticadas.
  - A porta 80 é usada apenas para baixar as CRLs (listas de certificados revogados) para garantir que nenhum dos certificados usados por esse recurso tenha sido revogado.
  - Para obter a lista completa dos requisitos de rede, consulte [Azure Active Directory autenticação de passagem: início rápido](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- As senhas que os usuários fornecem durante a entrada são criptografadas na nuvem antes que os agentes de autenticação local as aceitem para validação em relação à Active Directory.
- O canal HTTPS entre o Azure AD e o agente de autenticação local é protegido usando a autenticação mútua.
- O protege suas contas de usuário trabalhando diretamente com [as políticas de acesso condicional do Azure ad](../active-directory-conditional-access-azure-portal.md), incluindo a MFA (autenticação multifator), o [bloqueio de autenticação herdada](../conditional-access/conditions.md) e a [filtragem de ataques de senha de força bruta](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Componentes envolvidos

Para obter detalhes gerais sobre o Azure AD operacional, serviço e segurança de dados, consulte a [central de confiabilidade](https://azure.microsoft.com/support/trust-center/). Os seguintes componentes são envolvidos quando você usa a autenticação de passagem para entrada do usuário:
- **STS do Azure ad**: um STS (serviço de token de segurança) sem estado que processa solicitações de entrada e emite tokens de segurança para os navegadores, clientes ou serviços dos usuários, conforme necessário.
- **Barramento de serviço do Azure**: fornece comunicação habilitada para nuvem com mensagens corporativas e transmite comunicação que ajuda você a conectar soluções locais com a nuvem.
- **Azure ad Connect agente de autenticação**: um componente local que escuta e responde a solicitações de validação de senha.
- **Banco de dados SQL do Azure**: contém informações sobre os agentes de autenticação do locatário, incluindo seus metadados e chaves de criptografia.
- **Active Directory**: Active Directory local, onde suas contas de usuário e suas senhas são armazenadas.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalação e registro dos agentes de autenticação

Os agentes de autenticação são instalados e registrados com o Azure AD quando você:
   - [Habilitar a autenticação de passagem por meio do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Adicionar mais agentes de autenticação para garantir a alta disponibilidade de solicitações de entrada](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Obter um agente de autenticação que funcione envolve três fases principais:

1. Instalação do agente de autenticação
2. Registro do agente de autenticação
3. Inicialização do agente de autenticação

As seções a seguir discutem essas fases em detalhes.

### <a name="authentication-agent-installation"></a>Instalação do agente de autenticação

Somente os administradores globais podem instalar um agente de autenticação (usando Azure AD Connect ou autônomo) em um servidor local. A instalação adiciona duas novas entradas à lista de**programas e recursos** do **painel de controle**@no__t **-1 @no__t**-3:
- O próprio aplicativo do agente de autenticação. Esse aplicativo é executado com privilégios de [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) .
- O aplicativo de atualizador que é usado para atualizar automaticamente o agente de autenticação. Este aplicativo é executado com privilégios [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) .

### <a name="authentication-agent-registration"></a>Registro do agente de autenticação

Depois de instalar o agente de autenticação, ele precisa se registrar no Azure AD. O Azure AD atribui a cada agente de autenticação um certificado de identidade digital exclusivo que pode ser usado para comunicação segura com o Azure AD.

O procedimento de registro também associa o agente de autenticação ao seu locatário. Isso garante que o Azure AD saiba que esse agente de autenticação específico é o único autorizado a lidar com solicitações de validação de senha para seu locatário. Esse procedimento é repetido para cada novo agente de autenticação registrado.

Os agentes de autenticação usam as seguintes etapas para se registrarem com o Azure AD:

![Registro do agente](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. O Azure AD primeiro solicita que um administrador global entre no Azure AD com suas credenciais. Durante a entrada, o agente de autenticação adquire um token de acesso que pode ser usado em nome do administrador global.
2. Em seguida, o agente de autenticação gera um par de chaves: uma chave pública e uma chave privada.
    - O par de chaves é gerado por meio da criptografia padrão RSA de 2048 bits.
    - A chave privada permanece no servidor local onde reside o agente de autenticação.
3. O agente de autenticação faz uma solicitação de "registro" para o Azure AD por HTTPS, com os seguintes componentes incluídos na solicitação:
    - O token de acesso adquirido na etapa 1.
    - A chave pública gerada na etapa 2.
    - Uma solicitação de assinatura de certificado (CSR ou solicitação de certificado). Essa solicitação se aplica a um certificado de identidade digital, com o Azure AD como sua autoridade de certificação (CA).
4. O Azure AD valida o token de acesso na solicitação de registro e verifica se a solicitação provém de um administrador global.
5. Em seguida, o Azure AD assina e envia um certificado de identidade digital de volta para o agente de autenticação.
    - A AC raiz no Azure AD é usada para assinar o certificado. 

      > [!NOTE]
      > Essa autoridade de certificação _não_ está no repositório de autoridades de certificação raiz confiáveis do Windows.
    - A autoridade de certificação é usada somente pelo recurso de autenticação de passagem. A CA é usada apenas para assinar os representantes de logon durante o registro do agente de autenticação.
    -  Nenhum dos outros serviços do Azure AD usam essa autoridade de certificação.
    - A entidade do certificado (nome distinto ou DN) é definida como sua ID de locatário. Esse DN é um GUID que identifica exclusivamente seu locatário. Esse DN escopou o certificado para uso somente com seu locatário.
6. O Azure AD armazena a chave pública do agente de autenticação em um banco de dados SQL do Azure, ao qual somente o Azure AD tem acesso.
7. O certificado (emitido na etapa 5) é armazenado no servidor local no repositório de certificados do Windows (especificamente no local [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) ). Ele é usado pelo agente de autenticação e pelos aplicativos de atualizador.

### <a name="authentication-agent-initialization"></a>Inicialização do agente de autenticação

Quando o agente de autenticação é iniciado, seja pela primeira vez após o registro ou após uma reinicialização do servidor, ele precisa de uma maneira de se comunicar com segurança com o serviço do Azure AD e começar a aceitar solicitações de validação de senha.

![Inicialização do agente](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Veja como os agentes de autenticação são inicializados:

1. O agente de autenticação faz uma solicitação de inicialização de saída para o Azure AD. 
    - Essa solicitação é feita pela porta 443 e está em um canal HTTPS mutuamente autenticado. A solicitação usa o mesmo certificado que foi emitido durante o registro do agente de autenticação.
2. O Azure AD responde à solicitação fornecendo uma chave de acesso para uma fila do barramento de serviço do Azure que é exclusiva para seu locatário e que é identificada pela sua ID de locatário.
3. O agente de autenticação faz uma conexão HTTPS de saída persistente (pela porta 443) para a fila. 
    - O agente de autenticação agora está pronto para recuperar e manipular solicitações de validação de senha.

Se você tiver vários agentes de autenticação registrados em seu locatário, o procedimento de inicialização garantirá que cada um se conecte à mesma fila do barramento de serviço. 

## <a name="process-sign-in-requests"></a>Processar solicitações de entrada 

O diagrama a seguir mostra como a autenticação de passagem processa as solicitações de entrada do usuário.

![Processar entrada](./media/how-to-connect-pta-security-deep-dive/pta3.png)

A autenticação de passagem manipula uma solicitação de entrada do usuário da seguinte maneira: 

1. Um usuário tenta acessar um aplicativo, por exemplo, o [Outlook Web App](https://outlook.office365.com/owa).
2. Se o usuário ainda não estiver conectado, o aplicativo redirecionará o navegador para a página de entrada do Azure AD.
3. O serviço STS do Azure AD responde de volta com a página de **entrada do usuário** .
4. O usuário insere seu nome de usuário na página de **entrada de usuários** e seleciona o botão **Avançar** .
5. O usuário insere sua senha na página de **entrada do usuário** e seleciona o botão de **entrada** .
6. O nome de usuário e a senha são enviados para o STS do Azure AD em uma solicitação HTTPS POST.
7. O STS do Azure AD recupera chaves públicas para todos os agentes de autenticação registrados em seu locatário do banco de dados SQL do Azure e criptografa a senha usando-as.
    - Ele produz "N" valores de senha criptografados para agentes de autenticação "N" registrados em seu locatário.
8. O STS do Azure AD coloca a solicitação de validação de senha, que consiste nos valores de nome de usuário e senha criptografada, na fila do barramento de serviço específica para seu locatário.
9. Como os agentes de autenticação inicializados estão persistentemente conectados à fila do barramento de serviço, um dos agentes de autenticação disponíveis recupera a solicitação de validação de senha.
10. O agente de autenticação localiza o valor da senha criptografada que é específico para sua chave pública, usando um identificador e descriptografa-o usando sua chave privada.
11. O agente de autenticação tenta validar o nome de usuário e a senha no local Active Directory usando a [API LogonUser do Win32](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) com o parâmetro **DwLogonType** definido como **LOGON32_LOGON_NETWORK**. 
    - Essa API é a mesma API usada pelo Serviços de Federação do Active Directory (AD FS) (AD FS) para conectar usuários em um cenário de entrada federado.
    - Essa API depende do processo de resolução padrão no Windows Server para localizar o controlador de domínio.
12. O agente de autenticação recebe o resultado de Active Directory, como êxito, nome de usuário ou senha incorretos ou senha expirada.

   > [!NOTE]
   > Se o agente de autenticação falhar durante o processo de entrada, a solicitação de entrada inteira será descartada. Não há nenhuma entrega de solicitações de entrada de um agente de autenticação para outro agente de autenticação local. Esses agentes só se comunicam com a nuvem, e não entre si.
   
13. O agente de autenticação encaminha o resultado para o STS do Azure AD por meio de um canal HTTPS autenticado mutuamente de saída pela porta 443. A autenticação mútua usa o certificado emitido anteriormente para o agente de autenticação durante o registro.
14. O STS do Azure AD verifica se esse resultado está correlacionado com a solicitação de entrada específica em seu locatário.
15. O STS do Azure AD continua com o procedimento de entrada conforme configurado. Por exemplo, se a validação de senha foi bem-sucedida, o usuário pode ser desafiado pela autenticação multifator ou Redirecionado de volta para o aplicativo.

## <a name="operational-security-of-the-authentication-agents"></a>Segurança operacional dos agentes de autenticação

Para garantir que a autenticação de passagem permaneça operacionalmente segura, o Azure AD renova periodicamente os certificados dos agentes de autenticação. O Azure AD dispara as renovações. As renovações não são governadas pelos próprios agentes de autenticação.

![Segurança operacional](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Para renovar a confiança de um agente de autenticação com o Azure AD:

1. O agente de autenticação executa ping periodicamente do Azure AD a cada poucas horas para verificar se é hora de renovar seu certificado. O certificado é renovado 30 dias antes de sua expiração.
    - Essa verificação é feita em um canal HTTPS mutuamente autenticado e usa o mesmo certificado que foi emitido durante o registro.
2. Se o serviço indicar que é hora de renovar, o agente de autenticação gerará um novo par de chaves: uma chave pública e uma chave privada.
    - Essas chaves são geradas por meio da criptografia padrão RSA de 2048 bits.
    - A chave privada nunca deixa o servidor local.
3. O agente de autenticação faz uma solicitação de "renovação de certificado" para o Azure AD por HTTPS, com os seguintes componentes incluídos na solicitação:
    - O certificado existente que é recuperado do local CERT_SYSTEM_STORE_LOCAL_MACHINE no repositório de certificados do Windows. Não há um administrador global envolvido neste procedimento, portanto, não há nenhum token de acesso necessário em nome do administrador global.
    - A chave pública gerada na etapa 2.
    - Uma solicitação de assinatura de certificado (CSR ou solicitação de certificado). Essa solicitação se aplica a um novo certificado de identidade digital, com o Azure AD como sua autoridade de certificação.
4. O Azure AD valida o certificado existente na solicitação de renovação de certificado. Em seguida, ele verifica se a solicitação veio de um agente de autenticação registrado em seu locatário.
5. Se o certificado existente ainda for válido, o Azure AD assinará um novo certificado de identidade digital e emitirá o novo certificado de volta para o agente de autenticação. 
6. Se o certificado existente tiver expirado, o Azure AD excluirá o agente de autenticação da lista de agentes de autenticação registrados do seu locatário. Em seguida, um administrador global precisa instalar e registrar manualmente um novo agente de autenticação.
    - Use a autoridade de certificação raiz do Azure AD para assinar o certificado.
    - Defina a entidade do certificado (nome distinto ou DN) para sua ID de locatário, um GUID que identifica exclusivamente seu locatário. O DN tem como escopo o certificado somente para seu locatário.
6. O Azure AD armazena a nova chave pública do agente de autenticação em um banco de dados SQL do Azure ao qual apenas ele tem acesso. Ele também invalida a chave pública antiga associada ao agente de autenticação.
7. O novo certificado (emitido na etapa 5) é armazenado no servidor no repositório de certificados do Windows (especificamente no local [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) ).
    - Como o procedimento de renovação de confiança ocorre de forma não interativa (sem a presença do administrador global), o agente de autenticação não tem mais acesso para atualizar o certificado existente no local CERT_SYSTEM_STORE_LOCAL_MACHINE. 
    
   > [!NOTE]
   > Esse procedimento não remove o certificado em si do local CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. O novo certificado é usado para autenticação deste ponto em diante. Cada renovação subsequente do certificado substitui o certificado no local CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Atualização automática dos agentes de autenticação

O aplicativo atualizador atualiza automaticamente o agente de autenticação quando uma nova versão (com correções de bugs ou aprimoramentos de desempenho) é lançada. O aplicativo do atualizador não trata nenhuma solicitação de validação de senha para seu locatário.

O Azure AD hospeda a nova versão do software como um **pacote de Windows Installer assinado (MSI)** . O MSI é assinado usando o [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) com SHA256 como o algoritmo Digest. 

![Atualização automática](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Para atualizar automaticamente um agente de autenticação:

1. O aplicativo atualizador executa ping do Azure AD a cada hora para verificar se há uma nova versão do agente de autenticação disponível. 
    - Essa verificação é feita em um canal HTTPS mutuamente autenticado usando o mesmo certificado que foi emitido durante o registro. O agente de autenticação e o atualizador compartilham o certificado armazenado no servidor.
2. Se uma nova versão estiver disponível, o Azure AD retornará o MSI assinado de volta para o atualizador.
3. O atualizador verifica se o MSI está assinado pela Microsoft.
4. O atualizador executa o MSI. Essa ação envolve as seguintes etapas:

   > [!NOTE]
   > O atualizador é executado com privilégios de [sistema local](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) .

    - Interrompe o serviço do agente de autenticação
    - Instala a nova versão do agente de autenticação no servidor
    - Reinicia o serviço do agente de autenticação

>[!NOTE]
>Se você tiver vários agentes de autenticação registrados em seu locatário, o Azure AD não renovará seus certificados nem os atualizará ao mesmo tempo. Em vez disso, o Azure AD faz isso, um de cada vez, para garantir a alta disponibilidade de solicitações de entrada.
>


## <a name="next-steps"></a>Passos seguintes
- [Limitações atuais](how-to-connect-pta-current-limitations.md): saiba quais cenários têm suporte e quais não são.
- [Início rápido](how-to-connect-pta-quick-start.md): colocar em funcionamento a autenticação de passagem do Azure AD.
- [Migrar de AD FS para autenticação de passagem](https://aka.ms/adfstoptadpdownload) – um guia detalhado para migrar do AD FS (ou outras tecnologias de Federação) para a autenticação de passagem.
- [Bloqueio inteligente](../authentication/howto-password-smart-lockout.md): Configure o recurso de bloqueio inteligente em seu locatário para proteger as contas de usuário.
- [Como funciona](how-to-connect-pta-how-it-works.md): Aprenda as noções básicas de como funciona a autenticação de passagem do Azure AD.
- [Perguntas](how-to-connect-pta-faq.md)frequentes: Encontre respostas para perguntas frequentes.
- [Solução de problemas](tshoot-connect-pass-through-authentication.md): saiba como resolver problemas comuns com o recurso de autenticação de passagem.
- [SSO contínuo do Azure ad](how-to-connect-sso.md): Saiba mais sobre esse recurso complementar.
