---
title: Concluir uma implantação de autenticação sem senha com o Azure AD
description: Saiba como planear e implementar uma implementação de autenticação sem palavras-passe do Diretório Ativo Azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4566c8ec58677589c044d79560bffa7616294d70
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505615"
---
# <a name="complete-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Complete uma implementação de autenticação sem palavra-passe no Diretório Ativo do Azure

A maior parte do ataques cibernéticos começa com um nome de usuário e senha roubados de alguém em uma organização. As organizações tentam combater a ameaça, exigindo que os utilizadores utilizem uma das seguintes abordagens:

- Senhas longas
- Senhas complexas
- Alterações frequentes de senha
- Multi-Factor Authentication (MFA)

A pesquisa da Microsoft [mostra](https://aka.ms/passwordguidance) que estes esforços irritam os utilizadores e impulsionam os custos de suporte. Para mais informações, consulte [o seu pa$$word não importa.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

A implantação de autenticação com senha oferece os seguintes benefícios:

- Maior segurança. Reduza o risco de ataques de phishing e de irrigação de senha removendo senhas como uma superfície de ataque.
- Melhor experiência do usuário. Dê aos utilizadores uma forma conveniente de aceder a dados de qualquer lugar, e fornecer fácil acesso a aplicações e serviços como Outlook, OneDrive ou Office enquanto está móvel.
- Ideias robustas. Obter informações sobre a atividade de usuários sem senha com log e auditoria robustos.

Com senha sem palavras-passe, a palavra-passe é substituída por algo que tem mais algo que é ou algo que sabe. Por exemplo, no Windows Hello for Business, um gesto biométrico, como um rosto ou impressão digital, ou um PIN específico do dispositivo que não é transmitido através de uma rede.

A Microsoft oferece três opções de autenticação com senha que abrangem muitos cenários. Estes métodos podem ser utilizados em conjunto:

- [O Windows Hello for Business](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) é o melhor para os utilizadores nos seus computadores Windows dedicados.
- O início de sessão de segurança com chaves de [segurança FIDO2](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) é especialmente útil para os utilizadores que se inscrevam em máquinas partilhadas como quiosques, em situações em que o uso de telefones é restrito e para identidades altamente privilegiadas.
- O registo telefónico com a [aplicação Microsoft Authenticator](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) é útil para fornecer uma opção sem palavras-passe aos utilizadores com dispositivos móveis. A aplicação Authenticator transforma qualquer iOS ou telefone Android numa credencial forte e sem palavras-passe, permitindo que os utilizadores assinem em qualquer plataforma ou navegador. Os utilizadores fazem o insessão recebendo uma notificação para o seu telemóvel, correspondendo a um número apresentado no ecrã com o do seu telemóvel e, em seguida, usando os seus dados biométricos ou PIN para confirmar.

## <a name="compare-passwordless-authentication-methods"></a>Comparar métodos de autenticação com senha

Os métodos de autenticação sem palavras-passe da Microsoft permitem diferentes cenários. Considere suas necessidades organizacionais, pré-requisitos e os recursos de cada método de autenticação para selecionar sua estratégia de autenticação com senha. Recomendamos que todas as organizações que usam dispositivos Windows 10 usem o Windows Hello para empresas. Em seguida, adicione o registo do telefone (com a aplicação Microsoft Authenticator) ou as chaves de segurança para cenários adicionais.

### <a name="passwordless-authentication-scenarios"></a>Cenários de autenticação com senha

| Cenário | Autenticação por telefone | Chaves de segurança | Windows Hello para Empresas |
| --- | --- | --- | --- |
| **Sinal de computador em:** <br> Do dispositivo Windows 10 atribuído | **Não** | **Sim** <br> Com biométrica, PIN | **Sim**<br>com reconhecimento biométrico e PIN |
| **Sinal de computador em:** <br> Do dispositivo Windows 10 compartilhado | **Não** | **Sim** <br> Com biométrica, PIN  | **Não** |
| **Sessão de inscrição na aplicação web:** <br>de um computador dedicado ao usuário | **Sim** | **Sim** <br> Fornecido um único sessão para apps é ativado por sessão de computador | **Sim**<br> Fornecido um único sessão para apps é ativado por sessão de computador |
| **Sessão de inscrição na aplicação web:** <br> de um dispositivo móvel ou não Windows | **Sim** | **Não** | **Não** |
| **Sinal de computador em:** <br> Computador não Windows | **Não** | **Não** | **Não** |

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Considerações técnicas para o aplicativo Microsoft Authenticator

**Integração AD FS** - Quando um utilizador ativa a credencial sem palavras-passe do Microsoft Autenticador, a autenticação para esse utilizador não envia uma notificação para aprovação. Os utilizadores de um inquilino híbrido estão impedidos de serem direcionados para a ADFS para iniciar sessão, a menos que selecionem "Use a sua palavra-passe em vez disso". Este processo também contorna quaisquer políticas de Acesso Condicional no local e fluxos de autenticação pass-through. No entanto, se for especificado um *login_hint,* o utilizador é encaminhado para a ADFS e contorna a opção de utilização da credencial sem palavras-passe.

**Servidor Azure MFA** - Utilizadores finais habilitados para MFA através do servidor Azure MFA de uma organização pode criar e usar uma única credencial de sinal de telefone sem palavras-passe. Se o utilizador tentar atualizar várias instalações (5 ou mais) do Autenticador Microsoft com a credencial, esta alteração pode resultar num erro.

**Registo do Dispositivo** - Para utilizar a aplicação Autenticadorpara autenticação sem palavras-passe, o dispositivo deve ser registado no inquilino da AD Azure e não pode ser um dispositivo partilhado. Um dispositivo só pode ser registrado em um único locatário. Este limite significa que apenas uma conta de trabalho ou escola é suportada para o registo telefónico utilizando a aplicação Authenticator.

## <a name="prerequisites"></a>Pré-requisitos

As organizações devem cumprir os seguintes pré-requisitos antes de iniciar uma implementação sem palavras-passe:

| Pré-requisito | Aplicação autenticadora | Chaves de segurança do FIDO2 |
| --- | --- | --- |
| O [registo combinado para O MFA azure e redefinição de senha de autosserviço (SSPR)](howto-registration-mfa-sspr-combined.md) está ativado (função de pré-visualização) | √ | √ |
| [Os utilizadores são capazes de executar O MFA Azure](howto-mfa-getstarted.md) | √ | √ |
| [Os utilizadores registaram-se no Azure MFA e SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Os utilizadores registaram os seus dispositivos móveis no Azure Ative Directory](../devices/overview.md) | √ |   |
| Windows 10 versão 1809 ou superior usando um navegador com suporte como Microsoft Edge ou Mozilla Firefox <br> (versão 67 ou superior). <br> *A Microsoft recomenda a versão 1903 ou superior para suporte nativo.* |   | √ |
| Chaves de segurança FIDO2 compatíveis. Certifique-se de que está a utilizar um dispositivo de segurança FIDO2 testado e verificado pela [Microsoft,](howto-authentication-passwordless-enable.md) ou outro dispositivo de segurança FIDO2 compatível. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Pré-requisitos para o Windows Hello para empresas

Os pré-requisitos para o Windows Hello dependem muito do facto de estar a implementar numa configuração apenas no local, híbrida ou em nuvem. Para mais informações, consulte a [lista completa de pré-requisitos para o Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Multi-Factor Authentication do Azure

Os usuários registram seu método com senha como parte do fluxo de registro do Azure MFA. A autenticação multifator com um nome de usuário e senha junto com outro método registrado pode ser usada como um fallback, caso eles não possam usar seu telefone ou chave de segurança em alguns cenários.

### <a name="security-key-lifecycle"></a>Ciclo de vida da chave de segurança

As chaves de segurança habilitam o acesso aos seus recursos e você deve planejar o gerenciamento desses dispositivos físicos.

1. Distribuição chave: Planeie como fornecer chaves para a sua organização. Você pode ter um processo de provisionamento centralizado ou permitir que os usuários finais adquiram chaves compatíveis com FIDO 2,0.
1. Ativação de chave: os usuários finais devem ativar a chave de segurança automaticamente. Os utilizadores finais registam as suas chaves de segurança em [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) e ativam o segundo fator (PIN ou biométrico) na primeira utilização.
1. Desabilitando uma chave: embora a funcionalidade de chave de segurança esteja no estágio de visualização, não há como um administrador remover uma chave de uma conta de usuário. O usuário deve removê-lo. Se uma chave for perdida ou roubada:
   1. Remova o usuário de qualquer grupo habilitado para autenticação com senha.
   1. Verifique se eles removeram a chave como um método de autenticação.
   1. Emitir uma nova chave.
1. Substituição de chave: os usuários podem habilitar duas chaves de segurança ao mesmo tempo. Ao substituir uma chave de segurança, verifique se o usuário também removeu a chave que está sendo substituída.

### <a name="enable-windows-10-support"></a>Habilitar o suporte do Windows 10

Ativar o acesso ao Windows 10 utilizando chaves de segurança FIDO2 requer que a funcionalidade do fornecedor de credenciais no Windows 10 seja de uma das seguintes formas:

- [Ativar provedor de credenciais com Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - A implantação do Intune é a opção recomendada.
- [Permitir o prestador de credenciais com um pacote de provisionamento](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Se a implantação do Intune não for possível, os administradores deverão implantar um pacote em cada computador para habilitar a funcionalidade do provedor de credenciais. A instalação do pacote pode ser executada por uma das seguintes opções:
      - Política de Grupo ou Configuration Manager
      - Instalação local em um computador com Windows 10
- [Ativar prestador de credenciais com política de grupo](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Apenas suportados para dispositivos híbridos Azure AD.

#### <a name="enable-on-premises-integration"></a>Permitir a integração no local

Para permitir o acesso aos recursos no local, siga os passos para permitir o sinal de chave de [segurança sem palavras-passe para os recursos no local (pré-visualização)](howto-authentication-passwordless-security-key-on-premises.md).

> [!IMPORTANT]
> Estes passos também devem ser concluídos para qualquer dispositivo híbrido Azure AD para utilizar as chaves de segurança FIDO2 para o windows 10 iniciar sessão.

### <a name="register-security-keys"></a>Registrar chaves de segurança

Os utilizadores devem registar a sua chave de segurança em cada um dos seus Diretórios Ativos Azure juntou-se às máquinas do Windows 10.

Para mais informações, consulte o [registo do Utilizador e a gestão das chaves de segurança FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).

### <a name="licensing-for-passwordless-authentication"></a>Licenciamento para autenticação com senha

Não há nenhum custo adicional para autenticação sem senha, embora alguns pré-requisitos possam exigir uma assinatura premium. Consulte informações detalhadas sobre funcionalidades e licenciamentos na página de licenciamento do [Diretório Ativo Azure](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="develop-a-plan"></a>Desenvolver um plano

Considere suas necessidades de negócios e os casos de uso para cada método de autenticação. Em seguida, selecione o método que melhor atenda às suas necessidades.

### <a name="use-cases"></a>Casos de utilização

A tabela a seguir descreve os casos de uso a serem implementados durante este projeto.

| Área | Descrição |
| --- | --- |
| **Acesso** | O insessão sem palavras-passe está disponível a partir de um dispositivo corporativo ou pessoal dentro ou fora da rede corporativa. |
| **Auditoria** | Os dados de uso estão disponíveis para que os administradores sejam auditados quase em tempo real. <br> Os dados de uso são baixados em sistemas corporativos pelo menos a cada 29 dias ou a ferramenta SIEM é usada. |
| **Governação** | O ciclo de vida das atribuições de usuário ao método de autenticação apropriado e aos grupos associados é definido e monitorado. |
| **Segurança** | O acesso ao método de autenticação apropriado é controlado por meio de atribuições de usuário e grupo. <br> Apenas os utilizadores autorizados podem utilizar o sessão sem palavras-passe. |
| **Performance** (Desempenho) | As linhas do tempo de propagação de atribuição de acesso são documentadas e monitoradas. <br> As horas de entrada são medidas para facilitar o uso. |
| **Experiência do Utilizador** | Os usuários estão cientes da compatibilidade com a mobilidade. <br> Os utilizadores podem configurar a aplicação Authenticator sem palavras-passe. |
| **Suporte** | Os utilizadores estão cientes de como encontrar suporte para problemas de entrada sem palavras-passe. |

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, é tipicamente devido a expectativas desajustadas sobre impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) de que está a envolver as partes interessadas certas e que os papéis das partes interessadas no projeto são bem compreendidos.

### <a name="organization-communications"></a>Comunicações da organização

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique-se proativamente como a experiência dos usuários será alterada, quando ele será alterado e como obter suporte se eles tiverem problemas.

As suas comunicações para utilizadores finais devem incluir as seguintes informações:

- [Permitir a experiência combinada de registo de segurança](howto-authentication-passwordless-phone.md)
- [Descarregar a aplicação Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Registo na aplicação Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Inscreva-se com o seu telefone](../user-help/user-help-auth-app-sign-in.md)

A Microsoft fornece modelos de [comunicação](https://aka.ms/mfatemplates)MFA, [modelos](https://www.microsoft.com/download/details.aspx?id=56768)de comunicação de reset de palavra-passe self-service (SSPR) e [documentação de utilizador final](../user-help/security-info-setup-signin.md) para ajudar a redigir as suas comunicações. Pode enviar utilizadores para [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) para se registarem diretamente selecionando os links **de Informação** de Segurança nessa página.

### <a name="testing-passwordless"></a>Testando com senha

Em cada fase da sua implementação enquanto testa cenários e adoção, certifique-se de que os resultados são como esperado.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testando o aplicativo Microsoft Authenticator

Seguem-se os casos de teste de amostrapara autenticação sem palavras-passe com a aplicação Microsoft Authenticator:

| Cenário | Resultados esperados |
| --- | --- |
| O usuário pode registrar Microsoft Authenticator aplicativo | O usuário pode registrar o aplicativo do aka.ms/mysecurityinfo |
| O utilizador pode ativar o registo do telefone | Entrada pelo telefone configurada para a conta corporativa |
| O utilizador pode aceder a uma aplicação com acesso telefónico | O utilizador passa pelo fluxo de entrada do telefone e atinge a aplicação designada. |
| Teste o registo de registo de sinal de telefone redirecionado desligando o microsoft authenticator sem palavras-passe dentro do ecrã dos métodos de autenticação no portal Do Diretório Ativo Azure | Anteriormente, os utilizadores não podem utilizar o acesso sem palavras-passe ao Microsoft Authenticator. |
| Remover o registo do telefone da aplicação Microsoft Authenticator | A conta de trabalho não está mais disponível no Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Testando chaves de segurança

Veja a seguir exemplos de casos de teste para autenticação sem senha com chaves de segurança.

**Sem palavras-passe fido sintetizado para o Diretório Ativo Azure Juntou-se aos dispositivos do Windows 10**

| Cenário | Resultados esperados |
| --- | --- |
| O usuário pode registrar o dispositivo FIDO2 (1809) | O usuário pode registrar o dispositivo FIDO2 usando as configurações > contas > opções de entrada > chave de segurança |
| O usuário pode redefinir o dispositivo FIDO2 (1809) | O usuário pode redefinir o dispositivo FIDO2 usando o software do fabricante |
| O usuário pode entrar com o dispositivo FIDO2 (1809) | O utilizador pode selecionar a Chave de Segurança a partir da janela de iniciar sessão e iniciar sessão com sucesso. |
| O usuário pode registrar o dispositivo FIDO2 (1903) | O usuário pode registrar o dispositivo FIDO2 em Configurações > contas > opções de entrada > chave de segurança |
| O usuário pode redefinir o dispositivo FIDO2 (1903) | O usuário pode redefinir o dispositivo FIDO2 nas configurações > contas > opções de entrada > chave de segurança |
| O usuário pode entrar com o dispositivo FIDO2 (1903) | O utilizador pode selecionar a Chave de Segurança a partir da janela de iniciar sessão e iniciar sessão com sucesso. |

**Sem palavras-passe fido sintetizar para web apps Azure AD**

| Cenário | Resultados esperados |
| --- | --- |
| O usuário pode registrar o dispositivo FIDO2 em aka.ms/mysecurityinfo usando o Microsoft Edge | O registro deve ter sucesso |
| O usuário pode registrar o dispositivo FIDO2 em aka.ms/mysecurityinfo usando o Firefox | O registro deve ter sucesso |
| O usuário pode entrar no OneDrive online usando o dispositivo FIDO2 usando o Microsoft Edge | O inato deve ter sucesso |
| O usuário pode entrar no OneDrive online usando o dispositivo FIDO2 usando o Firefox | O inato deve ter sucesso |
| Teste a reversão do registro de dispositivo FIDO2 desativando as chaves de segurança FIDO2 na folha métodos de autenticação no portal Azure Active Directory | Os utilizadores serão solicitados a iniciar sessão utilizando a sua chave de segurança, irá inseri-los com sucesso e será demonstrado um erro: "A política da empresa requer que utilize um método diferente para iniciar sessão". Os usuários devem ser capazes de selecionar um método diferente e entrar com êxito. Feche a janela e entre novamente para verificar se elas não veem a mesma mensagem de erro. |

### <a name="auditing-passwordless"></a>Auditando com senha

O Azure AD tem relatórios que fornecem informações técnicas e de negócios. Seus proprietários de aplicativos técnicos e de negócios assumem a propriedade e consomem esses relatórios com base nos requisitos da sua organização.

A secção de métodos de **Autenticação** dentro do portal de Diretório Ativo Azure é onde os administradores podem ativar e gerir definições para credenciais sem palavras-passe.

O Azure AD adiciona entradas aos logs de auditoria quando:

- Um administrador faz alterações na seção métodos de autenticação.
- Um usuário faz qualquer tipo de alteração em suas credenciais dentro de Azure Active Directory.

A tabela que se segue fornece alguns exemplos de cenários típicos de reporte:

|   | Faça a gestão do risco. | Aumentar a produtividade | Governação e conformidade |
| --- | --- | --- | --- |
| **Tipos de relatório** | Métodos de autenticação – usuários registrados para o registro de segurança combinado | Métodos de autenticação – usuários registrados para notificação de aplicativo | Entradas: revise quem está acessando o locatário e como |
| **Ações potenciais** | Usuários de destino ainda não registrados | Impulsionar a adoção de Microsoft Authenticator aplicativo ou chaves de segurança | Revogar o acesso ou impor políticas de segurança adicionais para administradores |

A **Azure AD retém** a maioria dos dados de auditoria durante 30 dias e disponibiliza os dados através do portal Azure Admin ou da API para que possa descarregar nos seus sistemas de análise. Se a sua organização necessitar de uma retenção mais longa, os registos precisam de ser exportados e consumidos numa ferramenta SIEM como [o Azure Sentinel,](../../sentinel/connect-azure-active-directory.md)Splunk ou Sumo Logic. [Saiba mais sobre visualizar os seus relatórios de acesso e utilização.](../reports-monitoring/overview-reports.md)

Os utilizadores podem registar-se e gerir as suas credenciais navegando para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Esse link direciona os usuários para a experiência de gerenciamento de credenciais do usuário final que foi habilitada por meio da experiência combinada de registro SSPR/MFA. Qualquer registro de dispositivos de segurança FIDO2 ou alterações nos métodos de autenticação por um usuário é registrado no Azure Active Directory logs de auditoria.

Quando os utilizadores ativam ou desativam a conta numa chave de segurança, ou reiniciam o segundo fator para a chave de segurança nas suas máquinas Windows 10, uma entrada é adicionada ao registo de segurança e estão sob os seguintes IDs do evento: *4670* e *5382*.

### <a name="plan-for-rollback"></a>Plano para retrocesso

Embora a autenticação sem senha seja um recurso leve com impacto mínimo sobre os usuários finais, pode ser necessário reverter.

A reversão requer que o administrador inscreva no portal azure Ative Directory, selecione os métodos de autenticação forte desejados e altere a opção de ativação para **Nº**. Este processo desliga a funcionalidade sem palavras-passe para todos os utilizadores.

Os utilizadores que já tenham registado dispositivos de segurança FIDO2 são solicitados a utilizar o dispositivo de segurança no seu próximo início de sessão e, em seguida, ver o seguinte erro:

![Escolha uma maneira diferente de entrar](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

### <a name="plan-to-pilot"></a>Planejar para o piloto

Ao implantar a autenticação com senha, você deve primeiro habilitar um ou mais grupos pilotos. Pode [criar grupos](../fundamentals/active-directory-groups-create-azure-portal.md) especificamente para este fim. Adicione os usuários que farão parte do piloto para os grupos. Em seguida, habilite novos métodos de autenticação com senha para os grupos selecionados.

Os grupos podem ser sincronizados de um diretório local ou do Azure AD. Uma vez satisfeito com os resultados do seu piloto, pode ligar a autenticação sem palavras-passe para todos os utilizadores.

Consulte [as melhores práticas para um piloto](https://aka.ms/deploymentplans) na página de planos de implantação.

## <a name="deploy-passwordless-authentication"></a>Implantar autenticação com senha

Siga as etapas alinhadas ao método escolhido abaixo.

### <a name="required-administrative-roles"></a>Funções administrativas necessárias

| Função do Azure AD | Descrição |
| --- | --- |
| Administrador de Autenticação | Papel menos privilegiado capaz de implementar e gerir métodos de autenticação. |
| Utilizador | Função menos privilegiada para configurar a aplicação Authenticator no dispositivo ou para inscrever o dispositivo chave de segurança para entrada na Web ou Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Implementar o registo do telefone com a aplicação Microsoft Authenticator

Siga os passos do artigo, Ative o [acesso sem palavras-passe com a aplicação Microsoft Authenticator](howto-authentication-passwordless-phone.md) para ativar a aplicação Microsoft Authenticator como um método de autenticação sem palavras-passe na sua organização.

### <a name="deploy-fido2-security-key-sign-in"></a>Implementar o sinal de sinal de chave de segurança FIDO2

Siga os passos do artigo, Ative o sinal de chave de [segurança sem palavras-passe para o Azure AD](howto-authentication-passwordless-security-key.md) para permitir as chaves de segurança FIDO2 como métodos de autenticação sem palavras-passe na sua organização.

### <a name="troubleshoot-phone-sign-in"></a>Sessão telefónica de resolução de problemas

| Cenário | Solução |
| --- | --- |
| O utilizador não pode efetuar o registo combinado. | Certifique-se de que o [registo combinado](concept-registration-mfa-sspr-combined.md) está ativado. |
| O utilizador não pode ativar a aplicação de autenticador de sinal de telefone. | Certifique-se de que o utilizador está no âmbito da implementação. |
| O utilizador NÃO está disponível para autenticação sem palavras-passe, mas é apresentado com opção de entrada sem palavras-passe, que não pode ser completada. | Esse cenário ocorre quando o usuário habilitou a entrada no telefone n do aplicativo antes da criação da política. <br> Para ativar o sessão: Adicione o utilizador ao âmbito dos utilizadores habilitado para iniciar sessão sem palavras-passe. <br> Para bloquear a entrada: faça com que o usuário remova seu formulário de credencial desse aplicativo. |

### <a name="troubleshoot-security-key-sign-in"></a>Sessão de segurança de resolução de problemas

| Cenário | Solução |
| --- | --- |
| O utilizador não pode efetuar o registo combinado. | Certifique-se de que o [registo combinado](concept-registration-mfa-sspr-combined.md) está ativado. |
| O utilizador não pode adicionar uma chave de segurança nas suas [definições](https://aka.ms/mysecurityinfo)de segurança . | Certifique-se de que [as chaves](howto-authentication-passwordless-security-key.md) de segurança estão ativadas. |
| O utilizador não pode adicionar a chave de segurança nas opções de sessão do Windows 10. | [Certifique-se de que as chaves de segurança para windows iniciar sessão](howto-authentication-passwordless-enable.md) |
| **Mensagem de erro**: Detetámos que este navegador ou SISTEMA não suporta as chaves de segurança FIDO2. | Os dispositivos de segurança FIDO2 sem senha só podem ser registrados em navegadores com suporte (Microsoft Edge, Firefox versão 67) no Windows 10 versão 1809 ou superior. |
| **Mensagem de erro**: A sua política da empresa requer que utilize um método diferente para iniciar sessão. | Não se esqueça de que as chaves de segurança estão habilitadas no locatário. |
| O usuário não pode gerenciar minha chave de segurança no Windows 10 versão 1809 | A versão 1809 requer que você use o software de gerenciamento de chaves de segurança fornecido pelo fornecedor de chave FIDO2. Contate o fornecedor para obter suporte. |
| Acho que a minha chave de segurança FIDO2 pode estar defeituosa, como posso testá-la. | Navegue para [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), introduza credenciais para uma conta de teste, ligue a chave de segurança do suspeito, selecione o botão **+** na parte superior direita do ecrã, clique em criar e passe pelo processo de criação. Se esse cenário falhar, o dispositivo poderá estar com defeito. |

## <a name="next-steps"></a>Passos Seguintes

- [Ativar chaves de segurança sem palavras-passe para iniciar sessão para AD Azure](howto-authentication-passwordless-security-key.md)
- [Ativar o acesso sem palavras-passe com a aplicação Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Saiba mais sobre o uso e insights dos métodos de autenticação](howto-authentication-methods-usage-insights.md)
