---
title: Planeie uma implementação de autenticação sem palavras-passe com Azure AD
description: Saiba como planear e implementar uma implementação de autenticação sem palavras-passe do Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f0c19e33ab6f91e69f9c7dbc5bc29fef1fd53bb
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964881"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Planeie uma implementação de autenticação sem palavras-passe no Azure Ative Directory

> [!NOTE]
> Para criar uma versão offline deste plano de implementação, use a impressão do seu navegador para a funcionalidade PDF.

A maioria dos ciberataques começa com um nome de utilizador comprometido e senha. As organizações tentam contrariar a ameaça exigindo que os utilizadores utilizem uma das seguintes abordagens:

- Senhas longas
- Senhas complexas
- Alterações frequentes de senha
- Multi-Factor Authentication (MFA)

A pesquisa da Microsoft [mostra](https://aka.ms/passwordguidance) que estes esforços irritam os utilizadores e impulsionam os custos de suporte. Para mais informações, consulte [o seu pa$$word não importa.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

### <a name="benefits-of-passwordless-authentication"></a>Benefícios da autenticação sem palavras-passe

- **Segurança acrescida.** Reduza o risco de ataques de phishing e spray de palavra-passe removendo palavras-passe como superfície de ataque.
-  **Melhor experiência do utilizador.** Dê aos utilizadores uma forma conveniente de aceder aos dados a partir de qualquer lugar. Fornecer fácil acesso a aplicações e serviços como Outlook, OneDrive ou Office enquanto móvel.
-  **Insights robustos.** Obtenha informações sobre a atividade sem palavras-passe dos utilizadores com registo robusto e auditoria.

Sem palavras-passe, a palavra-passe é substituída por algo que tem mais algo que é ou algo que sabe. Por exemplo, o Windows Hello for Business pode usar um gesto biométrico como um rosto ou impressão digital, ou um PIN específico do dispositivo que não é transmitido através de uma rede.

## <a name="passwordless-authentication-methods"></a>Métodos de autenticação sem palavras-passe
A Microsoft oferece três opções de autenticação sem palavras-passe que cobrem muitos cenários. Estes métodos podem ser utilizados em conjunto:

- [O Windows Hello for Business](./concept-authentication-passwordless.md) é o melhor para os utilizadores nos seus computadores Windows dedicados.
- O início de sposição da chave de segurança com [as teclas de segurança FIDO2](./concept-authentication-passwordless.md) é especialmente útil para os utilizadores que se inscrevem em máquinas partilhadas como quiosques, em situações em que o uso de telefones é restrito, e para identidades altamente privilegiadas.
- O acesso telefónico com a [aplicação Microsoft Authenticator](./concept-authentication-passwordless.md) é útil para fornecer uma opção sem palavras-passe aos utilizadores com dispositivos móveis. A aplicação Authenticator transforma qualquer iOS ou telefone Android numa credencial forte e sem palavras-passe, permitindo que os utilizadores assinem em qualquer plataforma ou navegador. Os utilizadores insinuam-se recebendo uma notificação para o seu telemóvel, correspondendo um número exibido no ecrã ao do telemóvel e, em seguida, usando os seus dados biométricos ou PIN para confirmar.

### <a name="passwordless-authentication-scenarios"></a>Cenários de autenticação sem palavras-passe

Os métodos de autenticação sem palavras-passe da Microsoft permitem diferentes cenários. Considere as suas necessidades organizacionais, pré-requisitos e as capacidades de cada método de autenticação para selecionar a sua estratégia de autenticação sem palavras-passe. Recomendamos que todas as organizações que utilizam dispositivos Windows 10 utilizem o Windows Hello para o Negócio. Em seguida, adicione o sent in do telefone (com a aplicação Microsoft Authenticator) ou as teclas de segurança para cenários adicionais.

| Cenário | Autenticação do telefone | Chaves de segurança | Windows Hello para empresas |
| --- | --- | --- | --- |
| **Sinal de computador em**: <br> Do dispositivo designado Windows 10 | **Não** | **Sim** <br> Com biométrico, PIN | **Sim**<br>com reconhecimento biométrico e ou PIN |
| **Sinal de computador em**: <br> A partir do dispositivo partilhado do Windows 10 | **Não** | **Sim** <br> Com biométrico, PIN  | **Não** |
| **Sindução de aplicações web:** <br>a partir de um computador dedicado ao utilizador | **Sim** | **Sim** <br> Fornecido um único sinal de inscrição para apps é ativado por iniciar sôm-in do computador | **Sim**<br> Fornecido um único sinal de inscrição para apps é ativado por iniciar sôm-in do computador |
| **Sindução de aplicações web:** <br> a partir de um dispositivo móvel ou não-janelas | **Sim** | **Não** | **Não** |
| **Sinal de computador em**: <br> Computador não-Windows | **Não** | **Não** | **Não** |

Para obter informações sobre a seleção do melhor método para a sua organização, consulte [decidir um método sem palavras-passe](./concept-authentication-passwordless.md#choose-a-passwordless-method).

## <a name="prerequisites"></a>Pré-requisitos

As organizações devem cumprir os seguintes requisitos antes de iniciar uma implementação sem palavras-passe:

| Pré-requisito | App autenticador | Chaves de segurança FIDO2 |
| --- | --- | --- |
| Está ativado [o registo combinado para a autenticação multi-factor Azure e o reset da palavra-passe de autosserviço (SSPR)](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Os utilizadores podem realizar a autenticação de vários fatores Azure](howto-mfa-getstarted.md) | √ | √ |
| [Os utilizadores registaram-se para autenticação de vários fatores Azure e SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Os utilizadores registaram os seus dispositivos móveis no Azure Ative Directory](../devices/overview.md) | √ |   |
| Versão 1809 ou superior do Windows 10 utilizando um navegador suportado como Microsoft Edge ou Mozilla Firefox <br> (versão 67 ou superior). <br> *A Microsoft recomenda a versão 1903 ou superior para suporte nativo*. |   | √ |
| Chaves de segurança FIDO2 compatíveis. Certifique-se de que está a utilizar um dispositivo de segurança FIDO2 [testado e verificado pela Microsoft](./concept-authentication-passwordless.md) ou outro dispositivo de segurança FIDO2 compatível. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Pré-requisitos para Windows Hello for Business

Os pré-requisitos para o Windows Hello são altamente dependentes de estar a implementar numa configuração no local, híbrida ou apenas em nuvem. Para mais informações, consulte a [listagem completa de pré-requisitos para o Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Multi-Factor Authentication do Azure

Os utilizadores registam o seu método sem palavra-passe como parte do fluxo de registo de autenticação de vários fatores Azure. A autenticação multi-factor com um nome de utilizador e palavra-passe juntamente com outro método registado pode ser usada como um recuo no caso de não poderem usar o seu telefone ou chave de segurança em alguns cenários.

### <a name="licensing"></a>Licensing 
Não existe um custo adicional para a autenticação sem palavra-passe, embora alguns pré-requisitos possam exigir uma subscrição premium. Para informações detalhadas sobre funcionalidades e licenciamento na página de licenciamento do [Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/). 

## <a name="develop-a-plan"></a>Desenvolver um plano

Considere as necessidades do seu negócio e os casos de utilização para cada método de autenticação. Em seguida, selecione o método que melhor se adequa às suas necessidades.

### <a name="use-cases"></a>Casos de utilização

O quadro seguinte descreve os casos de utilização a implementar durante este projeto.

| Área | Descrição |
| --- | --- |
| **Acesso** | O sing-in sem palavra-passe está disponível a partir de um dispositivo corporativo ou pessoal dentro ou fora da rede corporativa. |
| **Auditoria** | Os dados de utilização estão disponíveis para os administradores auditar em tempo real. <br> Os dados de utilização são descarregados em sistemas corporativos pelo menos a cada 29 dias, ou a ferramenta SIEM é usada. |
| **Governação** | O ciclo de vida das atribuições do utilizador ao método de autenticação adequado e aos grupos associados é definido e monitorizado. |
| **Segurança** | O acesso ao método de autenticação adequado é controlado através de atribuições de utilizador e grupo. <br> Apenas os utilizadores autorizados podem utilizar o sing-in sem palavra-passe. |
| **Desempenho** | Os prazos de propagação da atribuição de acesso são documentados e monitorizados. <br> O sinal em tempos é medido para facilitar a utilização. |
| **Experiência do Utilizador** | Os utilizadores estão cientes da compatibilidade móvel. <br> Os utilizadores podem configurar o sign-in sem palavras-passe da aplicação Authenticator. |
| **Suporte** | Os utilizadores estão cientes de como encontrar suporte para problemas de sing-in sem palavras-passe. |

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, é tipicamente devido a expectativas desajustadas no impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) e que as partes interessadas no projeto são bem compreendidas.

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunicar proativamente como a experiência dos utilizadores vai mudar, quando vai mudar, e como ganhar suporte se eles experimentarem problemas.

As suas comunicações aos utilizadores finais devem incluir as seguintes informações:

- [Habilitando a experiência de registo de segurança combinada](howto-authentication-passwordless-phone.md)
- [Descarregar a app Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Registo na aplicação Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Iniciar sessão com o seu telefone](../user-help/user-help-auth-app-sign-in.md)

A Microsoft fornece modelos de [comunicação](https://aka.ms/mfatemplates)de autenticação multi-factores , Self-Service modelos de comunicação de redefinição de [palavras-passe](https://www.microsoft.com/download/details.aspx?id=56768)(SSPR) e [documentação do utilizador final](../user-help/security-info-setup-signin.md) para ajudar a elaborar as suas comunicações. Pode enviar os utilizadores [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) para se registarem diretamente, selecionando os links **de Informações de Segurança** nessa página.

### <a name="plan-to-pilot"></a>Plano para piloto

Quando implementar a autenticação sem palavras-passe, deve primeiro ativar um ou mais grupos piloto. Pode [criar grupos](../fundamentals/active-directory-groups-create-azure-portal.md) especificamente para este fim. Adicione os utilizadores que participarão no piloto aos grupos. Em seguida, ative novos métodos de autenticação sem palavras-passe para os grupos selecionados.

Os grupos podem ser sincronizados a partir de um diretório no local, ou a partir de Azure AD. Uma vez satisfeito com os resultados do seu piloto, pode ligar a autenticação sem palavras-passe para todos os utilizadores.

Consulte [as melhores práticas para um piloto](https://aka.ms/deploymentplans) na página de planos de implantação.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Planeie a autenticação sem palavras-passe com a aplicação Microsoft Authenticator

A aplicação Microsoft Authenticator é um download gratuito da Google Play ou da Apple App Store. [Saiba mais sobre o download da aplicação Microsoft Authenticator.](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) Os utilizadores descarreguem a aplicação Microsoft Authenticator. e siga as instruções para ativar o sinal do telefone. 

Transforma qualquer iOS ou telefone Android numa credencial forte e sem palavras-passe. Os utilizadores inserem-se em qualquer plataforma ou navegador, recebendo uma notificação para o seu telemóvel, correspondendo um número exibido no ecrã ao do telemóvel e, em seguida, usando biometria ou PIN para confirmar. [Consulte os detalhes sobre o funcionamento da aplicação Microsoft Authenticator.](./concept-authentication-passwordless.md#microsoft-authenticator-app)

![iniciar sista com a app Authenticator](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Considerações técnicas para a aplicação Microsoft Authenticator

**Integração AD FS** - Quando um utilizador permite a credencial sem palavras-passe do Microsoft Authenticator, a autenticação para esse utilizador não pode enviar uma notificação para aprovação. Os utilizadores de um inquilino híbrido estão impedidos de serem direcionados para a ADFS para iniciar seduca, a menos que selecionem "Use a sua palavra-passe em vez disso". Este processo também contorna quaisquer políticas de acesso condicional no local e fluxos de autenticação de passagem. No entanto, se for especificada uma *login_hint,* o utilizador é encaminhado para a ADFS e contorna a opção de utilização da credencial sem palavra-passe.

**Servidor de autenticação multi-factor Azure** - Os utilizadores finais habilitados para a autenticação de vários fatores através do servidor Azure MFA de uma organização podem criar e utilizar uma única credencial de acesso telefónico sem palavras-passe. Se o utilizador tentar atualizar várias instalações (5 ou mais) do Autenticador Microsoft com a credencial, esta alteração pode resultar num erro.

**Registo do Dispositivo** - Para utilizar a aplicação Authenticator para autenticação sem palavras-passe, o dispositivo deve estar registado no inquilino Azure AD e não pode ser um dispositivo partilhado. Um dispositivo só pode ser registado num único inquilino. Este limite significa que apenas uma conta de trabalho ou escola é suportada para o início de sômedão telefónico utilizando a aplicação Authenticator.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Planeie a autenticação sem palavras-passe com chaves de segurança FIDO2
Existem três tipos de implementações de entrada sem palavra-passe disponíveis com chaves de segurança:

-    Aplicativos web Azure Ative Directory em um navegador suportado
-    Azure Ative Directory juntou-se a dispositivos Windows 10
-    O Diretório Ativo Híbrido Azure juntou-se a dispositivos Windows 10 (pré-visualização)
     -    Fornece acesso tanto à nuvem como aos recursos das instalações. Para obter mais informações sobre o acesso aos recursos no local, consulte [sSO para recursos no local utilizando chaves FIDOP2](./howto-authentication-passwordless-security-key-on-premises.md)

Tem de ativar **as teclas de segurança FIDO2 compatíveis.** A Microsoft anunciou [parcerias-chave com os principais fornecedores fido2.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)

**Para aplicações web AD AD Azure e Azure AD Windows unidos dispositivos:**

-    Versão 1809 ou superior do Windows 10 utilizando um navegador suportado como o Microsoft Edge ou o Mozilla Firefox (versão 67 ou superior). 
-    A versão 1809 do Windows 10 suporta o acesso fido2 e pode exigir a implementação do software do fabricante da chave FIDO2. Recomendamos que utilize a versão 1903 ou mais tarde. 

**Para dispositivos híbridos Azure Ative Directory Domain:** 
-    Versão do Windows 10 2004 ou posterior
-    Servidores de domínio totalmente remendados com o Windows Server 2016 ou 2019.
-    Versão mais recente do Azure AD Connect

Para obter uma lista completa de requisitos, consulte [Ativar o acesso da chave de segurança sem palavras-passe aos dispositivos Windows 10 com o Azure Ative Directory](./howto-authentication-passwordless-security-key-windows.md#requirements).


### <a name="security-key-life-cycle"></a>Ciclo de vida chave de segurança

As chaves de segurança permitem o acesso aos seus recursos e deve planear a gestão desses dispositivos físicos.

1. **Distribuição chave**: Planeie como providenciar chaves à sua organização. Pode ter um processo centralizado de provisionamento ou permitir que os utilizadores finais comprem chaves compatíveis com FIDO 2.0.
1. **Ativação da chave**: Os utilizadores finais devem ativar automaticamente a chave de segurança. Os utilizadores finais registam as suas chaves de segurança [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) e permitem o segundo fator (PIN ou biométrico) à primeira utilização.
1. **Desativação de uma chave**: Embora a funcionalidade da chave de segurança esteja na fase de pré-visualização, não há forma de um administrador remover uma chave de uma conta de utilizador. O utilizador deve removê-lo. Se uma chave for perdida ou roubada:
   1. Remova o utilizador de qualquer grupo ativado para autenticação sem palavras-passe.
   1. Verifique se removeram a chave como método de autenticação.
   1. Emita uma nova chave. **Substituição da chave**: Os utilizadores podem ativar duas chaves de segurança ao mesmo tempo. Ao substituir uma chave de segurança, certifique-se de que o utilizador também removeu a chave que está a ser substituída.

### <a name="enable-windows-10-support"></a>Ativar o suporte ao Windows 10

Permitir o sessão de sessão do Windows 10 utilizando as teclas de segurança FIDO2 requer ativar a funcionalidade do fornecedor de credenciais no Windows 10. Escolha uma das seguintes opções:

- [Ativar o fornecedor de credenciais com a Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - A implantação intune é a opção recomendada.
- [Permitir o fornecedor de credenciais com um pacote de provisionamento](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Se a implementação do Intune não for possível, os administradores devem implementar um pacote em cada máquina para permitir a funcionalidade do fornecedor de credenciais. A instalação da embalagem pode ser realizada por uma das seguintes opções:
      - Gestor de Política ou Configuração de Grupo
      - Instalação local numa máquina Do Windows 10
- [Permitir fornecedor de credenciais com Política de Grupo](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Suportado apenas para dispositivos híbridos Azure AD.

#### <a name="enable-on-premises-integration"></a>Permitir a integração no local

Para permitir o acesso aos recursos no local, siga as etapas para Permitir o [sinal da chave de segurança sem palavras-passe para os recursos no local (pré-visualização)](howto-authentication-passwordless-security-key-on-premises.md).

> [!IMPORTANT]
> Estes passos também devem ser preenchidos para qualquer dispositivo híbrido Azure AD associado para utilizar chaves de segurança FIDO2 para o windows 10 iniciar sação.

### <a name="register-security-keys"></a>Registar chaves de segurança

Os utilizadores devem registar a sua chave de segurança em cada um dos seus Azure Ative Directory que aderiram às máquinas do Windows 10.

Para mais informações, consulte [o registo e gestão do registo do Utilizador e a gestão das chaves de segurança FIDO2.](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)


## <a name="plan-auditing-security-and-testing"></a>Planear auditoria, segurança e testes
O planeamento para a auditoria que satisfaça os seus quadros organizacionais e de conformidade é uma parte essencial da sua implantação.

### <a name="auditing-passwordless"></a>Auditoria sem palavras-passe

A Azure AD tem relatórios que fornecem informações técnicas e empresariais. Os proprietários de aplicações empresariais e técnicas assumam a propriedade e consumam estes relatórios com base nos requisitos da sua organização.

A secção de métodos **de autenticação** dentro do portal Azure Ative Directory é onde os administradores podem ativar e gerir definições para credenciais sem palavras-passe.

A Azure AD adiciona entradas aos registos de auditoria quando:

- Um administrador faz alterações na secção métodos de autenticação.
- Um utilizador faz qualquer tipo de alteração às suas credenciais dentro do Azure Ative Directory.

A tabela a seguir apresenta alguns exemplos de cenários típicos de reporte:

|   | Faça a gestão do risco. | Aumentar a produtividade | Governação e conformidade |
| --- | --- | --- | --- |
| **Tipos de relatório** | Métodos de autenticação- utilizadores registados para registo de segurança combinada | Métodos de autenticação – utilizadores registados para notificação de aplicações | Inscrições: revisão de quem está a aceder ao inquilino e como |
| **Ações potenciais** | Utilizadores-alvo ainda não registados | Impulsionar a adoção de app microsoft authenticator ou chaves de segurança | Revogar o acesso ou impor políticas de segurança adicionais para administradores |

**O Azure AD mantém a maioria dos dados de auditoria durante 30 dias** e disponibiliza os dados através do portal Admin Azure ou da API para que possa descarregar nos seus sistemas de análise. Se necessitar de retenção, exportação e consumo de troncos numa ferramenta SIEM como [Azure Sentinel,](../../sentinel/connect-azure-active-directory.md)Splunk ou Sumo Logic. [Saiba mais sobre a visualização dos seus relatórios de acesso e utilização.](../reports-monitoring/overview-reports.md)

Os utilizadores podem registar-se e gerir as suas credenciais navegando para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Esta ligação direciona os utilizadores para a experiência de gestão de credenciais de utilizador final que foi ativada através da experiência combinada de registo de autenticação SSPR/Multi-factor. A Azure AD regista o registo de dispositivos de segurança FIDO2 e altera os métodos de autenticação por um utilizadores.

### <a name="plan-security"></a>Segurança do plano
Como parte deste plano de lançamento, a Microsoft recomenda que a autenticação sem palavras-passe seja ativada para todas as contas de administração privilegiadas.

Quando os utilizadores ativam ou desativam a conta numa chave de segurança, ou reiniciam o segundo fator para a chave de segurança nas suas máquinas Windows 10, uma entrada é adicionada ao registo de segurança e encontra-se nos seguintes IDs de evento: *4670* e *5382*.

### <a name="plan-testing"></a>Testes de plano

Em cada fase da sua implantação, à medida que testa cenários e adoção, certifique-se de que os resultados são esperados.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testar a aplicação Microsoft Authenticator

Seguem-se os casos de teste de amostra para autenticação sem palavras-passe com a aplicação Microsoft Authenticator:

| Cenário | Resultados esperados |
| --- | --- |
| O utilizador pode registar a aplicação Microsoft Authenticator | O utilizador pode registar-se a partir de aka.ms/mysecurityinfo |
| O utilizador pode ativar o registo do telefone | Sinal de telefone configurado para conta de trabalho |
| O utilizador pode aceder a uma aplicação com o acesso ao telefone | O utilizador passa pelo fluxo de entrada de telefone e atinge a aplicação. |
| Teste o registo de entrada de telefone de reversão desligando o teste de entrada sem palavras-passe do Microsoft Authenticator dentro do ecrã de métodos de autenticação no portal Azure Ative Directory | Os utilizadores previamente habilitados não podem utilizar o sing-in sem palavras-passe do Microsoft Authenticator. |
| Remoção do sent-in de telefone da aplicação Microsoft Authenticator | Conta de trabalho já não disponível no Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Testar chaves de segurança

Seguem-se os casos de teste de amostra para autenticação sem palavra-passe com chaves de segurança.

**Entrada FIDO sem palavras-passe para Azure Ative Directory Juntou-se a dispositivos Windows 10**

| Cenário | Resultados esperados |
| --- | --- |
| O utilizador pode registar o dispositivo FIDO2 (1809) | O utilizador pode registar o dispositivo FIDO2 utilizando as Definições > Contas > assinar nas opções > Chave de Segurança |
| O utilizador pode reiniciar o dispositivo FIDO2 (1809) | O utilizador pode redefinir o dispositivo FIDO2 utilizando o software do fabricante |
| O utilizador pode iniciar sedução com dispositivo FIDO2 (1809) | O utilizador pode selecionar a Chave de Segurança a partir da janela de inscrição e iniciar sins com sucesso. |
| O utilizador pode registar o dispositivo FIDO2 (1903) | O utilizador pode registar o dispositivo FIDO2 nas Definições > Contas > assinar nas opções > Chave de Segurança |
| O utilizador pode reiniciar o dispositivo FIDO2 (1903) | O utilizador pode redefinir o dispositivo FIDO2 nas Definições > Contas > assinar nas opções > Chave de Segurança |
| O utilizador pode iniciar sedução com dispositivo FIDO2 (1903) | O utilizador pode selecionar a Chave de Segurança a partir da janela de inscrição e iniciar sins com sucesso. |

**FiDO sem palavras-passe in para aplicações web AZure AD**

| Cenário | Resultados esperados |
| --- | --- |
| O utilizador pode registar o dispositivo FIDO2 em aka.ms/mysecurityinfo utilizando o Microsoft Edge | O registo deve ter sucesso |
| O utilizador pode registar o dispositivo FIDO2 no aka.ms/mysecurityinfo utilizando o Firefox | O registo deve ter sucesso |
| O utilizador pode iniciar sedução no OneDrive online utilizando o dispositivo FIDO2 utilizando o Microsoft Edge | O s-in deve ter sucesso |
| O utilizador pode iniciar sedução no OneDrive online utilizando o dispositivo FIDO2 utilizando o Firefox | O s-in deve ter sucesso |
| Teste o registo do dispositivo FIDO2 desligando as chaves de segurança FIDO2 dentro da janela do método de autenticação no portal Azure Ative Directory | Os utilizadores serão solicitados a iniciar seduções usando a sua chave de segurança. Os utilizadores iniciarão seduca com sucesso e será apresentado um erro: "A política da empresa requer que utilize um método diferente para iniciar seduca". Os utilizadores devem então ser capazes de selecionar um método diferente e iniciar sôs com sucesso. Feche a janela e inscreva-se novamente para verificar se não vê a mesma mensagem de erro. |

### <a name="plan-for-rollback"></a>Plano de reversão

Embora a autenticação sem palavras-passe seja uma característica leve com o mínimo impacto nos utilizadores finais, pode ser necessário retroceda.

O recuo requer que o administrador inscreva-se no portal Azure Ative Directory, selecione os métodos de autenticação forte desejados e altere a opção de ativação para **Nº**. Este processo descolou a funcionalidade sem palavras-passe para todos os utilizadores.

Os utilizadores que já registaram dispositivos de segurança FIDO2 são solicitados a utilizar o dispositivo de segurança na sua próxima sindução e, em seguida, ver o seguinte erro:

![escolher uma maneira diferente de se inscrever](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Implantação e resolução de problemas sem palavras-passe autenticação

Siga os passos alinhados com o método escolhido abaixo.

### <a name="required-administrative-roles"></a>Funções administrativas necessárias

| Papel de Azure AD | Descrição |
| --- | --- |
| Administrador Global|Papel menos privilegiado capaz de implementar experiência de registo combinado. |
| Administrador de Autenticação | Papel menos privilegiado capaz de implementar e gerir métodos de autenticação. |
| Utilizador | Papel menos privilegiado para configurar a aplicação Authenticator no dispositivo ou para inscrever o dispositivo chave de segurança para web ou início de sismo do Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Implementar o sôs-in telefónico com a aplicação Microsoft Authenticator

Siga os passos do artigo, Ative o [teste de saúde sem palavras-passe com a aplicação Microsoft Authenticator](howto-authentication-passwordless-phone.md) para ativar a aplicação Microsoft Authenticator como um método de autenticação sem palavras-passe na sua organização.

### <a name="deploy-fido2-security-key-sign-in"></a>Implementar o sinal de chave de segurança FIDO2

Siga os passos do artigo, Ative o [sinal da chave de segurança sem palavras-passe para a Azure AD](howto-authentication-passwordless-security-key.md) para ativar as chaves de segurança FIDO2 como métodos de autenticação sem palavras-passe.

### <a name="troubleshoot-phone-sign-in"></a>Sessão de resolução de problemas de sessão telefónica

| Cenário | Solução |
| --- | --- |
| O utilizador não pode efetuar o registo combinado. | Certifique-se de que o [registo combinado](concept-registration-mfa-sspr-combined.md) está ativado. |
| O utilizador não pode ativar a aplicação de autenticação de inscrição no telefone. | Certifique-se de que o utilizador está em possibilidade de implantação. |
| O utilizador não está no âmbito da autenticação sem palavras-passe, mas é apresentado com opção de entrada sem palavra-passe, que não conseguem completar. | Este cenário ocorre quando o utilizador ativou a inscrição do telefone na aplicação antes da criação da política. <br> *Para ativar o sing in*: Adicione o utilizador ao âmbito de utilizadores ativados para a entrada sem palavras-passe. <br> *Para bloquear o sessão:* que o utilizador retire a sua credencial dessa aplicação. |

### <a name="troubleshoot-security-key-sign-in"></a>Sessão de segurança de resolução de problemas de lançamento

| Cenário | Solução |
| --- | --- |
| O utilizador não pode efetuar o registo combinado. | Certifique-se de que o [registo combinado](concept-registration-mfa-sspr-combined.md) está ativado. |
| O utilizador não pode adicionar uma chave de segurança nas suas [definições de segurança](https://aka.ms/mysecurityinfo). | Certifique-se de que [as chaves de segurança](howto-authentication-passwordless-security-key.md) estão ativadas. |
| O utilizador não pode adicionar a chave de segurança nas opções de insinusição do Windows 10. | [Certifique-se de que as chaves de segurança para o Windows iniciar sinscrevam](./concept-authentication-passwordless.md) |
| **Error message**: Detetámos que este navegador ou SISTEMA não suporta chaves de segurança FIDO2. | Os dispositivos de segurança FIDO2 sem palavras-passe apenas podem ser registados em navegadores suportados (Microsoft Edge, versão Firefox 67) na versão 1809 do Windows 10 ou superior. |
| **Error message**: A sua política da empresa requer que utilize um método diferente para iniciar sôm. | Chaves de segurança inseguras estão ativadas no inquilino. |
| Utilizador incapaz de gerir a minha chave de segurança na versão 1809 do Windows 10 | A versão 1809 requer que utilize o software de gestão de chaves de segurança fornecido pelo fornecedor-chave FIDO2. Contacte o fornecedor para obter apoio. |
| Acho que a minha chave de segurança FIDO2 pode estar defeituosa, como posso testá-la. | Navegue para [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/) , introduza credenciais para uma conta de teste, ligue a chave de segurança do suspeito, selecione **+** o botão no topo direito do ecrã, clique em criar e passe pelo processo de criação. Se este cenário falhar, o seu dispositivo pode estar defeituoso. |

## <a name="next-steps"></a>Passos seguintes

- [Ativar chaves de segurança sem palavra-passe para iniciar sing para Azure AD](howto-authentication-passwordless-security-key.md)
- [Ativar o acesso sem palavras-passe com a aplicação Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Saiba mais sobre a utilização dos métodos de autenticação & insights](howto-authentication-methods-usage-insights.md)
