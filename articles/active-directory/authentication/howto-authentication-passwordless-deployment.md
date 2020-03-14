---
title: Planeie uma implementação de autenticação sem palavra-passe com a AD Azure
description: Saiba como planear e implementar uma implementação de autenticação sem palavras-passe do Diretório Ativo Azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 145439ebedd2ddf7c081339146010c66f37fe1af
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136539"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Planeie uma implementação de autenticação sem palavras-passe no Diretório Ativo do Azure

> [!NOTE]
> Para criar uma versão offline deste plano de implementação, use a funcionalidade Print do seu navegador para PDF.

A maioria dos ciberataques começa com um nome de utilizador comprometido e senha. As organizações tentam combater a ameaça, exigindo que os utilizadores utilizem uma das seguintes abordagens:

- Palavras-passe longas
- Senhas complexas
- Alterações frequentes de palavra-passe
- Multi-Factor Authentication (MFA)

A pesquisa da Microsoft [mostra](https://aka.ms/passwordguidance) que estes esforços irritam os utilizadores e impulsionam os custos de suporte. Para mais informações, consulte [o seu pa$$word não importa.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

### <a name="benefits-of-passwordless-authentication"></a>Benefícios da autenticação sem palavras-passe

- **Segurança acrescida.** Reduza o risco de phishing e ataques de spray de senha removendo senhas como uma superfície de ataque.
-  **Melhor experiência do utilizador.** Dê aos utilizadores uma forma conveniente de aceder aos dados de qualquer lugar. Fornecer fácil acesso a aplicações e serviços como Outlook, OneDrive ou Office enquanto está móvel.
-  **Insights robustos.** Obtenha informações sobre a atividade sem palavras-passe dos utilizadores com registo robusto e auditoria.

Com senha sem palavras-passe, a palavra-passe é substituída por algo que tem mais algo que é ou algo que sabe. Por exemplo, o Windows Hello for Business pode usar um gesto biométrico como um rosto ou impressão digital, ou um PIN específico do dispositivo que não é transmitido através de uma rede.

## <a name="passwordless-authentication-methods"></a>Métodos de autenticação sem palavras-passe
A Microsoft oferece três opções de autenticação sem palavras-passe que cobrem muitos cenários. Estes métodos podem ser utilizados em conjunto:

- [O Windows Hello for Business](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) é o melhor para os utilizadores nos seus computadores Windows dedicados.
- O início de sessão de segurança com chaves de [segurança FIDO2](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) é especialmente útil para os utilizadores que se inscrevam em máquinas partilhadas como quiosques, em situações em que o uso de telefones é restrito e para identidades altamente privilegiadas.
- O registo telefónico com a [aplicação Microsoft Authenticator](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) é útil para fornecer uma opção sem palavras-passe aos utilizadores com dispositivos móveis. A aplicação Authenticator transforma qualquer iOS ou telefone Android numa credencial forte e sem palavras-passe, permitindo que os utilizadores assinem em qualquer plataforma ou navegador. Os utilizadores fazem o insessão recebendo uma notificação para o seu telemóvel, correspondendo a um número apresentado no ecrã com o do seu telemóvel e, em seguida, usando os seus dados biométricos ou PIN para confirmar.

### <a name="passwordless-authentication-scenarios"></a>Cenários de autenticação sem palavras-passe

Os métodos de autenticação sem palavras-passe da Microsoft permitem diferentes cenários. Considere as suas necessidades organizacionais, pré-requisitos e as capacidades de cada método de autenticação para selecionar a sua estratégia de autenticação sem palavras-passe. Recomendamos que todas as organizações que utilizam dispositivos Windows 10 utilizem o Windows Hello for Business. Em seguida, adicione o registo do telefone (com a aplicação Microsoft Authenticator) ou as chaves de segurança para cenários adicionais.

| Cenário | Autenticação do telefone | Chaves de segurança | Windows Hello para Empresas |
| --- | --- | --- | --- |
| **Sinal de computador em:** <br> Do dispositivo windows 10 atribuído | **Não** | **Sim** <br> Com biométrico, PIN | **Sim**<br>com reconhecimento biométrico e ou PIN |
| **Sinal de computador em:** <br> A partir do dispositivo Partilhado Windows 10 | **Não** | **Sim** <br> Com biométrico, PIN  | **Não** |
| **Sessão de inscrição na aplicação web:** <br>de um computador dedicado ao utilizador | **Sim** | **Sim** <br> Fornecido um único sessão para apps é ativado por sessão de computador | **Sim**<br> Fornecido um único sessão para apps é ativado por sessão de computador |
| **Sessão de inscrição na aplicação web:** <br> de um dispositivo móvel ou não-windows | **Sim** | **Não** | **Não** |
| **Sinal de computador em:** <br> Computador não Windows | **Não** | **Não** | **Não** |

Para obter informações sobre a seleção do melhor método para a sua organização, consulte [Decidir um método sem palavras-passe](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless#deciding-a-passwordless-method).

## <a name="prerequisites"></a>Pré-requisitos

As organizações devem cumprir os seguintes pré-requisitos antes de iniciar uma implementação sem palavras-passe:

| Pré-requisito | Aplicação autenticadora | Chaves de segurança FIDO2 |
| --- | --- | --- |
| O registo combinado para a autenticação de [multifactores Azure e o reset de palavra-passe self-service (SSPR)](howto-registration-mfa-sspr-combined.md) está ativado (função de pré-visualização) | √ | √ |
| [Os utilizadores podem realizar a autenticação de multifactor Azure](howto-mfa-getstarted.md) | √ | √ |
| [Os utilizadores registaram-se para autenticação multifactor Azure e SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Os utilizadores registaram os seus dispositivos móveis no Azure Ative Directory](../devices/overview.md) | √ |   |
| Windows 10 versão 1809 ou superior usando um navegador suportado como Microsoft Edge ou Mozilla Firefox <br> (versão 67 ou superior). <br> *A Microsoft recomenda a versão 1903 ou superior para suporte nativo.* |   | √ |
| Chaves de segurança FIDO2 compatíveis. Certifique-se de que está a utilizar um dispositivo de segurança FIDO2 testado e verificado pela [Microsoft,](howto-authentication-passwordless-enable.md) ou outro dispositivo de segurança FIDO2 compatível. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Pré-requisitos para Windows Hello for Business

Os pré-requisitos para o Windows Hello dependem muito do facto de estar a implementar numa configuração apenas no local, híbrida ou em nuvem. Para mais informações, consulte a [lista completa de pré-requisitos para o Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Multi-Factor Authentication do Azure

Os utilizadores registam o seu método sem palavras-passe como parte do fluxo de registo de autenticação de multifactor Estoque Azure. A autenticação de vários fatores com um nome de utilizador e uma palavra-passe juntamente com outro método registado podem ser usadas como recuo no caso de não poderem utilizar o telemóvel ou a chave de segurança em alguns cenários.

### <a name="licensing"></a>Licenciamento 
Não existe um custo adicional para a autenticação sem palavras-passe, embora alguns pré-requisitos possam exigir uma subscrição premium. Para informações detalhadas sobre recurso e licenciamento na página de licenciamento do [Diretório Ativo Azure.](https://azure.microsoft.com/pricing/details/active-directory/) 

## <a name="develop-a-plan"></a>Desenvolver um plano

Considere as necessidades do seu negócio e os casos de utilização para cada método de autenticação. Em seguida, selecione o método que melhor se adequa às suas necessidades.

### <a name="use-cases"></a>Casos de utilização

A tabela que se segue descreve os casos de utilização a implementar durante este projeto.

| Área | Descrição |
| --- | --- |
| **Acesso** | O insessão sem palavras-passe está disponível a partir de um dispositivo corporativo ou pessoal dentro ou fora da rede corporativa. |
| **Auditoria** | Os dados de utilização estão disponíveis para os administradores auditarem em tempo real. <br> Os dados de utilização são descarregados em sistemas corporativos pelo menos a cada 29 dias, ou a ferramenta SIEM é usada. |
| **Governação** | O ciclo de vida das atribuições do utilizador ao método de autenticação adequado e aos grupos associados é definido e monitorizado. |
| **Segurança** | O acesso ao método de autenticação adequado é controlado através de atribuições de utilizador e grupo. <br> Apenas os utilizadores autorizados podem utilizar o sessão sem palavras-passe. |
| **Performance** (Desempenho) | Os prazos de propagação da atribuição de acesso são documentados e monitorizados. <br> O sinal nos tempos é medido para facilitar a utilização. |
| **Experiência do Utilizador** | Os utilizadores estão cientes da compatibilidade móvel. <br> Os utilizadores podem configurar a aplicação Authenticator sem palavras-passe. |
| **Suporte** | Os utilizadores estão cientes de como encontrar suporte para problemas de entrada sem palavras-passe. |

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, é tipicamente devido a expectativas desajustadas sobre impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) de que está a envolver as partes interessadas certas e que os papéis das partes interessadas no projeto são bem compreendidos.

### <a name="plan-communications"></a>Plan communications

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunicar proativamente como a experiência dos utilizadores vai mudar, quando irá mudar, e como obter suporte se experimentarem problemas.

As suas comunicações para utilizadores finais devem incluir as seguintes informações:

- [Permitir a experiência combinada de registo de segurança](howto-authentication-passwordless-phone.md)
- [Descarregar a aplicação Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Registo na aplicação Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Inscreva-se com o seu telefone](../user-help/user-help-auth-app-sign-in.md)

A Microsoft fornece modelos de [comunicação](https://aka.ms/mfatemplates)de autenticação multifactor, [modelos](https://www.microsoft.com/download/details.aspx?id=56768)de comunicação de reset de palavra-passe self-service (SSPR) e [documentação de utilizador final](../user-help/security-info-setup-signin.md) para ajudar a redesenhar as suas comunicações. Pode enviar utilizadores para [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) para se registarem diretamente selecionando os links **de Informação** de Segurança nessa página.

### <a name="plan-to-pilot"></a>Plano para pilotar

Quando implementar a autenticação sem palavras-passe, deve primeiro ativar um ou mais grupos piloto. Pode [criar grupos](../fundamentals/active-directory-groups-create-azure-portal.md) especificamente para este fim. Adicione os utilizadores que participarão no piloto aos grupos. Em seguida, ative novos métodos de autenticação sem palavras-passe para os grupos selecionados.

Os grupos podem ser sincronizados a partir de um diretório no local, ou a partir de Azure AD. Uma vez satisfeito com os resultados do seu piloto, pode ligar a autenticação sem palavras-passe para todos os utilizadores.

Consulte [as melhores práticas para um piloto](https://aka.ms/deploymentplans) na página de planos de implantação.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Planeie a autenticação sem palavras-passe com a aplicação Microsoft Authenticator

A aplicação Microsoft Authenticator é um download gratuito do Google Play ou da Apple App Store. [Saiba mais sobre o download da aplicação Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Os utilizadores têm de descarregar a aplicação Microsoft Authenticator. e siga as instruções para ativar o sinal telefónico. 

Transforma qualquer iOS ou telefone Android numa credencial forte e sem palavras-passe. Os utilizadores fazem sessão em qualquer plataforma ou navegador, recebendo uma notificação para o seu telemóvel, correspondendo a um número apresentado no ecrã com o do seu telemóvel e, em seguida, usando biometria ou PIN para confirmar. [Veja mais detalhes sobre o funcionamento da aplicação Microsoft Authenticator](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless#user-using-microsoft-authenticator-for-passwordless-sign-in). 

![inscreva-se na aplicação Autenticadora](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Considerações técnicas para a aplicação Microsoft Authenticator

**Integração AD FS** - Quando um utilizador ativa a credencial sem palavras-passe do Microsoft Autenticador, a autenticação para esse utilizador não envia uma notificação para aprovação. Os utilizadores de um inquilino híbrido estão impedidos de serem direcionados para a ADFS para iniciar sessão, a menos que selecionem "Use a sua palavra-passe em vez disso". Este processo também contorna quaisquer políticas de Acesso Condicional no local e fluxos de autenticação pass-through. No entanto, se for especificado um *login_hint,* o utilizador é encaminhado para a ADFS e contorna a opção de utilização da credencial sem palavras-passe.

Servidor de **autenticação Azure Multi-factor** - Utilizadores finais habilitados para autenticação multi-factor através do servidor Azure MFA de uma organização no local podem criar e usar uma única credencial de sinal de telefone sem palavras-passe. Se o utilizador tentar atualizar várias instalações (5 ou mais) do Autenticador Microsoft com a credencial, esta alteração pode resultar num erro.

**Registo do Dispositivo** - Para utilizar a aplicação Autenticadorpara autenticação sem palavras-passe, o dispositivo deve ser registado no inquilino da AD Azure e não pode ser um dispositivo partilhado. Um dispositivo só pode ser registado num único inquilino. Este limite significa que apenas uma conta de trabalho ou escola é suportada para o registo telefónico utilizando a aplicação Authenticator.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Planear autenticação sem palavras-passe com chaves de segurança FIDO2
Existem três tipos de implementações de entrada sem palavras-passe disponíveis com chaves de segurança:

-    Aplicações web Azure Ative Diretório em um navegador suportado
-    Diretório Ativo Azure juntou dispositivos windows 10
-    Diretório Ativo Hybrid Azure Juntou dispositivos Windows 10 (pré-visualização)
     -    Proporciona acesso tanto à nuvem como aos recursos das instalações. Para obter mais informações sobre o acesso aos recursos no local, consulte [o SSO para os recursos no local utilizando as chaves FIDOP2](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)

Tem de ativar as chaves de **segurança FIDO2 compatíveis**. A Microsoft anunciou [parcerias-chave com os principais fornecedores fido2.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)

**Para aplicações web Azure AD e Azure AD Windows uniram dispositivos:**

-    A versão 1809 do Windows 10 ou superior utilizando um navegador suportado como o Microsoft Edge ou o Mozilla Firefox (versão 67 ou superior). 
-    A versão 1809 do Windows 10 suporta o acesso ao FIDO2 e pode exigir que o software do fabricante da chave FIDO2 seja implementado. Recomendamos que utilize a versão 1903 ou mais tarde. 

**Para dispositivos de domínio ativo híbrido Azure Ative Directory:** 
-    Windows 10 Insider construir 18945 ou mais tarde
-    Servidores de domínio totalmente remendados que executam o Windows Server 2016 ou 2019.
-    Versão mais recente do Azure AD Connect

Para obter uma lista completa de requisitos, consulte [Enable passwords security key sign-in para dispositivos Windows 10 com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements).


### <a name="security-key-life-cycle"></a>Ciclo de vida chave de segurança

As chaves de segurança permitem o acesso aos seus recursos, e deve planear a gestão desses dispositivos físicos.

1. **Distribuição chave**: Planeie como fornecer chaves à sua organização. Pode ter um processo de provisionamento centralizado ou permitir que os utilizadores finais comprem chaves compatíveis com OFIDO 2.0.
1. **Ativação da chave**: Os utilizadores finais devem autoactivar a chave de segurança. Os utilizadores finais registam as suas chaves de segurança em [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) e ativam o segundo fator (PIN ou biométrico) na primeira utilização.
1. **Desativar uma tecla**: Enquanto a funcionalidade da chave de segurança está na fase de pré-visualização, não há forma de um administrador remover uma chave de uma conta de utilizador. O utilizador deve removê-lo. Se uma chave for perdida ou roubada:
   1. Retire o utilizador de qualquer grupo habilitado para autenticação sem palavras-passe.
   1. Verifique se removeram a chave como um método de autenticação.
   1. Emita uma nova chave. **Substituição da chave**: Os utilizadores podem ativar duas teclas de segurança ao mesmo tempo. Ao substituir uma chave de segurança, certifique-se de que o utilizador também removeu a chave que está a ser substituída.

### <a name="enable-windows-10-support"></a>Ativar suporte ao Windows 10

Ativar o acesso ao Windows 10 utilizando chaves de segurança FIDO2 requer que permita a funcionalidade do fornecedor credencial no Windows 10. Escolha um dos seguintes:

- [Ativar provedor de credenciais com Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - A implementação intonizada é a opção recomendada.
- [Permitir o prestador de credenciais com um pacote de provisionamento](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Se a implementação intune não for possível, os administradores devem implementar um pacote em cada máquina para ativar a funcionalidade do fornecedor de credenciais. A instalação da embalagem pode ser realizada por uma das seguintes opções:
      - Política de Grupo ou Gestor de Configuração
      - Instalação local numa máquina do Windows 10
- [Ativar prestador de credenciais com política de grupo](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Apenas suportados para dispositivos híbridos Azure AD.

#### <a name="enable-on-premises-integration"></a>Permitir a integração no local

Para permitir o acesso aos recursos no local, siga os passos para permitir o sinal de chave de [segurança sem palavras-passe para os recursos no local (pré-visualização)](howto-authentication-passwordless-security-key-on-premises.md).

> [!IMPORTANT]
> Estes passos também devem ser concluídos para qualquer dispositivo híbrido Azure AD para utilizar as chaves de segurança FIDO2 para o windows 10 iniciar sessão.

### <a name="register-security-keys"></a>Registar chaves de segurança

Os utilizadores devem registar a sua chave de segurança em cada um dos seus Diretórios Ativos Azure juntou-se às máquinas do Windows 10.

Para mais informações, consulte o [registo do Utilizador e a gestão das chaves de segurança FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).


## <a name="plan-auditing-security-and-testing"></a>Planejamento de auditoria, segurança e testes
O planeamento de auditoria que satisfaça os seus quadros organizacionais e de conformidade é uma parte essencial da sua implantação.

### <a name="auditing-passwordless"></a>Auditoria sem palavras-passe

A Azure AD tem relatórios que fornecem informações técnicas e empresariais. Os proprietários de aplicações técnicas e empresariais assumem a propriedade e consomem estes relatórios com base nos requisitos da sua organização.

A secção de métodos de **Autenticação** dentro do portal de Diretório Ativo Azure é onde os administradores podem ativar e gerir definições para credenciais sem palavras-passe.

A Azure AD adiciona entradas nos registos de auditoria quando:

- Um administrador faz alterações na secção de métodos de Autenticação.
- Um utilizador faz qualquer tipo de alteração às suas credenciais dentro do Diretório Ativo Azure.

A tabela que se segue fornece alguns exemplos de cenários típicos de reporte:

|   | Faça a gestão do risco. | Aumentar a produtividade | Governação e conformidade |
| --- | --- | --- | --- |
| **Tipos de relatório** | Métodos de autenticação- utilizadores registados para registo de segurança combinado | Métodos de autenticação – utilizadores registados para notificação de apps | Inscrições: veja quem está a aceder ao inquilino e como |
| **Ações potenciais** | Utilizadores-alvo ainda não registados | Adoção de unidades de aplicativo sinuoso ou chave de segurança da Microsoft | Revogar ou impor políticas adicionais de segurança para os administradores |

A **Azure AD mantém** a maioria dos dados de auditoria durante 30 dias e disponibiliza os dados através do portal Azure Admin ou da API para que possa descarregar nos seus sistemas de análise. Se necessitar de retenção mais longa, exportar e consumir troncos numa ferramenta SIEM como [O Sentinela,](../../sentinel/connect-azure-active-directory.md)Splunk ou Sumo Logic. [Saiba mais sobre visualizar os seus relatórios de acesso e utilização.](../reports-monitoring/overview-reports.md)

Os utilizadores podem registar-se e gerir as suas credenciais navegando para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Este link direciona os utilizadores para a experiência de gestão da credencial do utilizador final que foi ativada através da experiência combinada de registo de autenticação SSPR/Multifactor. A Azure AD regista o registo de dispositivos de segurança FIDO2 e altera-se nos métodos de autenticação por parte de um utilizador.

### <a name="plan-security"></a>Planear a segurança
Como parte deste plano de lançamento, a Microsoft recomenda que a autenticação sem palavras-passe seja ativada para todas as contas de administração privilegiadas.

Quando os utilizadores ativam ou desativam a conta numa chave de segurança, ou reiniciam o segundo fator para a chave de segurança nas suas máquinas Windows 10, uma entrada é adicionada ao registo de segurança e estão sob os seguintes IDs do evento: *4670* e *5382*.

### <a name="plan-testing"></a>Teste de planos

Em cada fase da sua implementação enquanto testa cenários e adoção, certifique-se de que os resultados são como esperado.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testar a aplicação Autenticadora microsoft

Seguem-se os casos de teste de amostrapara autenticação sem palavras-passe com a aplicação Microsoft Authenticator:

| Cenário | Resultados esperados |
| --- | --- |
| O utilizador pode registar a aplicação Microsoft Authenticator | O utilizador pode registar a aplicação a partir de aka.ms/mysecurityinfo |
| O utilizador pode ativar o registo do telefone | Sinal de telefone configurado para conta de trabalho |
| O utilizador pode aceder a uma aplicação com acesso telefónico | O utilizador passa pelo fluxo de entrada de telefone e atinge a aplicação. |
| Teste o registo de registo de sinal de telefone redirecionado desligando o microsoft authenticator sem palavras-passe dentro do ecrã dos métodos de autenticação no portal Do Diretório Ativo Azure | Anteriormente, os utilizadores não podem utilizar o acesso sem palavras-passe ao Microsoft Authenticator. |
| Remover o registo do telefone da aplicação Microsoft Authenticator | Conta de trabalho já não está disponível no Autenticador microsoft |

#### <a name="testing-security-keys"></a>Testar chaves de segurança

Seguem-se os casos de teste da amostra para a autenticação sem palavras-passe com as chaves de segurança.

**Sem palavras-passe fido sintetizado para o Diretório Ativo Azure Juntou-se aos dispositivos do Windows 10**

| Cenário | Resultados esperados |
| --- | --- |
| O utilizador pode registar o dispositivo FIDO2 (1809) | O utilizador pode registar o dispositivo FIDO2 utilizando em Definições > Contas > iniciar sessão em opções > Chave de Segurança |
| O utilizador pode redefinir o dispositivo FIDO2 (1809) | O utilizador pode redefinir o dispositivo FIDO2 utilizando o software do fabricante |
| O utilizador pode iniciar sessão com o dispositivo FIDO2 (1809) | O utilizador pode selecionar a Chave de Segurança a partir da janela de iniciar sessão e iniciar sessão com sucesso. |
| O utilizador pode registar o dispositivo FIDO2 (1903) | O utilizador pode registar o dispositivo FIDO2 em Definições > Contas > iniciar sessão nas opções > Chave de Segurança |
| O utilizador pode redefinir o dispositivo FIDO2 (1903) | O utilizador pode redefinir o dispositivo FIDO2 em Definições > Contas > iniciar sessão em opções > Chave de Segurança |
| O utilizador pode iniciar sessão com o dispositivo FIDO2 (1903) | O utilizador pode selecionar a Chave de Segurança a partir da janela de iniciar sessão e iniciar sessão com sucesso. |

**Sem palavras-passe fido sintetizar para web apps Azure AD**

| Cenário | Resultados esperados |
| --- | --- |
| O utilizador pode registar o dispositivo FIDO2 em aka.ms/mysecurityinfo utilizando o Microsoft Edge | Inscrição deve ter sucesso |
| O utilizador pode registar dispositivo FIDO2 em aka.ms/mysecurityinfo utilizando o Firefox | Inscrição deve ter sucesso |
| O utilizador pode iniciar sessão no OneDrive online utilizando o dispositivo FIDO2 utilizando o Microsoft Edge | O inato deve ter sucesso |
| O utilizador pode iniciar sessão no OneDrive online utilizando o dispositivo FIDO2 utilizando o Firefox | O inato deve ter sucesso |
| Teste de reversão do registo do dispositivo FIDO2 desligando as teclas de segurança FIDO2 dentro da janela do método de autenticação no portal do Diretório Ativo Azure | Os utilizadores serão solicitados a iniciar sessão utilizando a sua chave de segurança. Os utilizadores iniciarão o seu instituto com sucesso e será apresentado um erro: "A política da empresa requer que utilize um método diferente para iniciar sessão". Os utilizadores devem então ser capazes de selecionar um método diferente e iniciar sessão com sucesso. Feche a janela e volte a iniciar sessão para verificar se não vê a mesma mensagem de erro. |

### <a name="plan-for-rollback"></a>Plano para retrocesso

Embora a autenticação sem palavra-passe seja uma funcionalidade leve com o mínimo impacto nos utilizadores finais, pode ser necessário recuar.

A reversão requer que o administrador inscreva no portal azure Ative Directory, selecione os métodos de autenticação forte desejados e altere a opção de ativação para **Nº**. Este processo desliga a funcionalidade sem palavras-passe para todos os utilizadores.

Os utilizadores que já tenham registado dispositivos de segurança FIDO2 são solicitados a utilizar o dispositivo de segurança no seu próximo início de sessão e, em seguida, ver o seguinte erro:

![escolher uma maneira diferente de assinar](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Implementar e resolver problemas de autenticação sem palavras-passe

Siga os passos alinhados com o seu método escolhido abaixo.

### <a name="required-administrative-roles"></a>Funções administrativas necessárias

| Papel azure ad | Descrição |
| --- | --- |
| Administrador Global|Papel menos privilegiado capaz de implementar experiência de registo combinado. |
| Administrador de Autenticação | Papel menos privilegiado capaz de implementar e gerir métodos de autenticação. |
| Utilizador | Função menos privilegiada para configurar a aplicação Authenticator no dispositivo ou para inscrever o dispositivo chave de segurança para entrada na Web ou Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Implementar o registo do telefone com a aplicação Microsoft Authenticator

Siga os passos do artigo, Ative o [acesso sem palavras-passe com a aplicação Microsoft Authenticator](howto-authentication-passwordless-phone.md) para ativar a aplicação Microsoft Authenticator como um método de autenticação sem palavras-passe na sua organização.

### <a name="deploy-fido2-security-key-sign-in"></a>Implementar o sinal de sinal de chave de segurança FIDO2

Siga os passos do artigo, Ative o sinal de chave de [segurança sem palavras-passe para o Azure AD](howto-authentication-passwordless-security-key.md) para ativar as chaves de segurança FIDO2 como métodos de autenticação sem palavras-passe.

### <a name="troubleshoot-phone-sign-in"></a>Sessão telefónica de resolução de problemas

| Cenário | Solução |
| --- | --- |
| O utilizador não pode efetuar o registo combinado. | Certifique-se de que o [registo combinado](concept-registration-mfa-sspr-combined.md) está ativado. |
| O utilizador não pode ativar a aplicação de autenticador de sinal de telefone. | Certifique-se de que o utilizador está no âmbito da implementação. |
| O utilizador NÃO está disponível para autenticação sem palavras-passe, mas é apresentado com opção de entrada sem palavras-passe, que não pode ser completada. | Este cenário ocorre quando o utilizador tem ativado o registo telefónico na aplicação antes da criação da apólice. <br> Para ativar o *sessão*: Adicione o utilizador ao âmbito dos utilizadores ativado para iniciar sessão sem palavras-passe. <br> Para bloquear o *sinal de inscrição*: mande o utilizador remover a sua credencial dessa aplicação. |

### <a name="troubleshoot-security-key-sign-in"></a>Sessão de segurança de resolução de problemas

| Cenário | Solução |
| --- | --- |
| O utilizador não pode realizar o registo combinado. | Certifique-se de que o [registo combinado](concept-registration-mfa-sspr-combined.md) está ativado. |
| O utilizador não pode adicionar uma chave de segurança nas suas [definições](https://aka.ms/mysecurityinfo)de segurança . | Certifique-se de que [as chaves](howto-authentication-passwordless-security-key.md) de segurança estão ativadas. |
| O utilizador não pode adicionar a chave de segurança nas opções de sessão do Windows 10. | [Certifique-se de que as chaves de segurança para windows iniciar sessão](howto-authentication-passwordless-enable.md) |
| **Mensagem de erro**: Detetámos que este navegador ou SISTEMA não suporta as chaves de segurança FIDO2. | Os dispositivos de segurança FIDO2 sem palavras-passe só podem ser registados em navegadores suportados (Microsoft Edge, versão 67 do Firefox) na versão 1809 do Windows 10 ou superior. |
| **Mensagem de erro**: A sua política da empresa requer que utilize um método diferente para iniciar sessão. | As chaves de segurança inseguras estão ativadas no inquilino. |
| Utilizador incapaz de gerir a minha chave de segurança na versão 1809 do Windows 1809 | A versão 1809 requer que utilize o software de gestão da chave de segurança fornecido pelo fornecedor chave FIDO2. Contacte o fornecedor para obter apoio. |
| Acho que a minha chave de segurança FIDO2 pode estar defeituosa, como posso testá-la. | Navegue para [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), introduza credenciais para uma conta de teste, ligue a chave de segurança do suspeito, selecione o botão **+** na parte superior direita do ecrã, clique em criar e passe pelo processo de criação. Se este cenário falhar, o seu dispositivo poderá estar defeituoso. |

## <a name="next-steps"></a>Passos Seguintes

- [Ativar chaves de segurança sem palavras-passe para iniciar sessão para AD Azure](howto-authentication-passwordless-security-key.md)
- [Ativar o acesso sem palavras-passe com a aplicação Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Saiba mais sobre o uso e insights dos métodos de autenticação](howto-authentication-methods-usage-insights.md)

