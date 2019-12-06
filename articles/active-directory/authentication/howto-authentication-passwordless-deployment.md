---
title: Concluir uma implantação de autenticação sem senha com o Azure AD
description: Concluir uma implantação de autenticação Azure Active Directory com senha
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eb8398decd1a447d0676195d6369cdc7e791e40
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848498"
---
# <a name="complete-a-passwordless-authentication-deployment"></a>Concluir uma implantação de autenticação com senha

A maior parte do ataques cibernéticos começa com um nome de usuário e senha roubados de alguém em uma organização. 

As organizações tentam combater a ameaça exigindo que os usuários usem:

- Senhas longas
- Senhas complexas
- Alterações frequentes de senha
- Multi-Factor Authentication (MFA)

A pesquisa da Microsoft [mostra](https://aka.ms/passwordguidance) que esses esforços irritam os usuários e impulsionam os custos de suporte. Para obter mais informações, consulte [seu PA $ $Word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

A implantação de autenticação com senha oferece os seguintes benefícios:

- Maior segurança. Reduza o risco de ataques de phishing e de irrigação de senha removendo senhas como uma superfície de ataque.
- Melhor experiência do usuário. Forneça aos usuários uma maneira conveniente de acessar dados de qualquer lugar e fornecer acesso fácil ao Outlook, OneDrive, Office e muito mais enquanto estiver móvel.
- Ideias robustas. Obter informações sobre a atividade de usuários sem senha com log e auditoria robustos.

A senha é substituída por algo que você tem, além de algo que você conhece. Por exemplo, no Windows Hello para empresas, um gesto biométrico, como uma face ou impressão digital, ou um PIN específico do dispositivo que não é transmitido por uma rede.

A Microsoft oferece três opções de autenticação com senha que abrangem muitos cenários. Esses métodos podem ser usados em conjunto. 

- O [Windows Hello para empresas](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) é melhor para os usuários em seus computadores Windows dedicados.
- A entrada de chave de segurança com [chaves de segurança FIDO2](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) é especialmente útil para os usuários que se conectam a computadores compartilhados, como quiosques, situações em que o uso de telefones é restrito e para identidades altamente privilegiadas.
- A entrada pelo telefone com o [aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) é útil para fornecer uma opção sem senha aos usuários com dispositivos móveis. Ele transforma qualquer telefone iOS ou Android em uma credencial forte e sem senha, permitindo que os usuários entrem em qualquer plataforma ou navegador. Os usuários entram obtendo uma notificação para seu telefone, correspondendo a um número exibido na tela para aquele em seu telefone e, em seguida, usando seus dados biométricos ou PIN para confirmar.

## <a name="compare-passwordless-authentication-methods"></a>Comparar métodos de autenticação com senha

Os métodos de autenticação com senha da Microsoft permitem diferentes cenários. Considere suas necessidades organizacionais, pré-requisitos e os recursos de cada método de autenticação para selecionar sua estratégia de autenticação com senha. Recomendamos que todas as organizações que usam dispositivos Windows 10 usem o Windows Hello para empresas. Em seguida, adicione uma entrada de telefone (com o aplicativo Microsoft Authenticator) ou chaves de segurança para cenários adicionais.

### <a name="passwordless-authentication-scenarios"></a>Cenários de autenticação com senha

| Cenário | Autenticação por telefone | Chaves de segurança | Windows Hello para Empresas |
| --- | --- | --- | --- |
| **Entrada do computador**: <br> Do dispositivo Windows 10 atribuído | **Não** | **Sim** <br> Com biométrica, PIN | **Sim**<br>com reconhecimento biométrico e PIN |
| **Entrada do computador**: <br> Do dispositivo Windows 10 compartilhado | **Não** | **Sim** <br> Com biométrica, PIN  | **Não** |
| **Entrada do aplicativo Web**: <br>de um computador dedicado ao usuário | **Sim** | **Sim** <br> O logon único fornecido para os aplicativos está habilitado pela entrada do computador | **Sim**<br> O logon único fornecido para os aplicativos está habilitado pela entrada do computador |
| **Entrada do aplicativo Web**: <br> de um dispositivo móvel ou não Windows | **Sim** | **Não** | **Não** |
| **Entrada do computador**: <br> Computador não Windows | **Não** | **Não** | **Não** |

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Considerações técnicas para o aplicativo Microsoft Authenticator

**Integração de AD FS** – quando um usuário habilita a Microsoft Authenticator credencial com senha, a autenticação para esse usuário assume o envio de uma notificação para aprovação. Os usuários em um locatário híbrido são impedidos de serem direcionados para o ADFS para entrar, a menos que eles selecionem "usar sua senha em vez disso". Esse processo também ignora qualquer política de acesso condicional local e fluxos de autenticação de passagem. No entanto, se um login_hint for especificado, o usuário será encaminhado para o ADFS e ignorará a opção de usar a credencial sem senha.

**Servidor MFA do Azure** -os usuários finais habilitados para MFA por meio do servidor Azure MFA local de uma organização ainda podem criar e usar uma única credencial de entrada de telefone sem senha. Se o usuário tentar atualizar várias instalações (5 +) do Microsoft Authenticator com a credencial, essa alteração poderá resultar em um erro.

**Registro de dispositivo** -para usar o aplicativo autenticador para autenticação com senha, o dispositivo deve ser registrado no locatário do Azure AD e não pode ser um dispositivo compartilhado. Um dispositivo só pode ser registrado em um único locatário. Esse limite significa que apenas uma conta corporativa ou de estudante tem suporte para a entrada pelo telefone usando o aplicativo autenticador.

## <a name="prerequisites"></a>Pré-requisitos

As organizações devem atender aos seguintes pré-requisitos antes de iniciar uma implantação com senha.

| Pré-requisito | Aplicativo autenticador | Chaves de segurança do FIDO2 |
| --- | --- | --- |
| O [registro combinado para MFA do Azure e redefinição de senha de autoatendimento (SSPR)](howto-registration-mfa-sspr-combined.md) está habilitado (recurso de visualização) | √ | √ |
| [Os usuários podem executar o Azure MFA](howto-mfa-getstarted.md) | √ | √ |
| [Os usuários se registraram para o Azure MFA e SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Os usuários registraram seus dispositivos móveis em Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 versão 1809 ou superior usando um navegador com suporte como Microsoft Edge ou Mozilla Firefox <br> (versão 67 ou superior). <br> *A Microsoft recomenda a versão 1903 ou superior para suporte nativo*. |   | √ |
| Chaves de segurança FIDO2 compatíveis. Verifique se você está usando um dispositivo de segurança FIDO2 [testado e verificado pela Microsoft](howto-authentication-passwordless-enable.md) ou outro dispositivo de segurança FIDO2 compatível. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Pré-requisitos para o Windows Hello para empresas

Os pré-requisitos para o Windows Hello são altamente dependentes se você estiver implantando em uma configuração local, híbrida ou somente na nuvem. Para obter mais informações, consulte a [lista completa de pré-requisitos para o Windows Hello para empresas](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Multi-Factor Authentication do Azure

Os usuários registram seu método com senha como parte do fluxo de registro do Azure MFA. A autenticação multifator com um nome de usuário e senha junto com outro método registrado pode ser usada como um fallback, caso eles não possam usar seu telefone ou chave de segurança em alguns cenários.

### <a name="security-key-lifecycle"></a>Ciclo de vida da chave de segurança

As chaves de segurança habilitam o acesso aos seus recursos e você deve planejar o gerenciamento desses dispositivos físicos.

1. Distribuição de chaves: planeje como você provisionará as chaves para sua organização. Você pode ter um processo de provisionamento centralizado ou permitir que os usuários finais adquiram chaves compatíveis com FIDO 2,0.
1. Ativação de chave: os usuários finais devem ativar a chave de segurança automaticamente. Os usuários finais registram suas chaves de segurança em [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) e habilitam o segundo fator (PIN ou biométrica) no primeiro uso.
1. Desabilitando uma chave: embora a funcionalidade de chave de segurança esteja no estágio de visualização, não há como um administrador remover uma chave de uma conta de usuário. O usuário deve removê-lo. Se uma chave for perdida ou roubada:
   1. Remova o usuário de qualquer grupo habilitado para autenticação com senha.
   1. Verifique se eles removeram a chave como um método de autenticação.
   1. Emitir uma nova chave.
1. Substituição de chave: os usuários podem habilitar duas chaves de segurança ao mesmo tempo. Ao substituir uma chave de segurança, verifique se o usuário também removeu a chave que está sendo substituída.

### <a name="enable-windows-10-support"></a>Habilitar o suporte do Windows 10

Habilitar a entrada do Windows 10 usando chaves de segurança FIDO2 requer a habilitação da funcionalidade do provedor de credenciais no Windows 10. Habilite-o de uma das duas maneiras:

- [Habilitar provedor de credenciais com o Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - A implantação do Intune é a opção recomendada.
- [Habilitar o provedor de credenciais com um pacote de provisionamento](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Se a implantação do Intune não for possível, os administradores deverão implantar um pacote em cada computador para habilitar a funcionalidade do provedor de credenciais. A instalação do pacote pode ser executada por uma das seguintes opções:
      - Política de Grupo ou System Center Configuration Manager (SCCM)
      - Instalação local em um computador com Windows 10

### <a name="register-security-keys"></a>Registrar chaves de segurança

Os usuários devem registrar sua chave de segurança em cada um de seus Azure Active Directory máquinas com Windows 10 Unidas.

Para obter mais informações, consulte [registro de usuário e gerenciamento de chaves de segurança FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).

### <a name="licensing-for-passwordless-authentication"></a>Licenciamento para autenticação com senha

Não há nenhum custo adicional para autenticação sem senha, embora alguns pré-requisitos possam exigir uma assinatura premium. Consulte informações detalhadas sobre recursos e licenciamento na [página Azure Active Directory Licensing](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="develop-a-plan"></a>Desenvolver um plano

Considere suas necessidades de negócios e os casos de uso para cada método de autenticação. Em seguida, selecione o método que melhor atenda às suas necessidades.

### <a name="use-cases"></a>Casos de utilização

A tabela a seguir descreve os casos de uso a serem implementados durante este projeto.

| Área | Descrição |
| --- | --- |
| **Acesso** | A conexão sem senha está disponível em um dispositivo corporativo ou pessoal dentro ou fora da rede corporativa. |
| **Auditoria** | Os dados de uso estão disponíveis para que os administradores sejam auditados quase em tempo real. <br> Os dados de uso são baixados em sistemas corporativos pelo menos a cada 29 dias ou a ferramenta SIEM é usada. |
| **Governação** | O ciclo de vida das atribuições de usuário ao método de autenticação apropriado e aos grupos associados é definido e monitorado. |
| **Segurança** | O acesso ao método de autenticação apropriado é controlado por meio de atribuições de usuário e grupo. <br> Somente usuários autorizados podem usar o logon sem senha. |
| **Performance** (Desempenho) | As linhas do tempo de propagação de atribuição de acesso são documentadas e monitoradas. <br> As horas de entrada são medidas para facilitar o uso. |
| **Experiência do usuário** | Os usuários estão cientes da compatibilidade com a mobilidade. <br> Os usuários podem configurar o logon sem senha do aplicativo autenticador. |
| **Suporte** | Os usuários estão cientes de como encontrar suporte para problemas de conexão sem senha. |

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, normalmente é devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [Verifique se você está participando dos participantes certos](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) e se as funções de Stakeholder no projeto são bem compreendidas.

### <a name="organization-communications"></a>Comunicações da organização

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique-se proativamente como a experiência dos usuários será alterada, quando ele será alterado e como obter suporte se eles tiverem problemas.

Suas comunicações com os usuários finais precisarão incluir:

- [Habilitando a experiência de registro de segurança combinada](howto-authentication-passwordless-phone.md)
- [Baixando o aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Registrando no aplicativo Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Entrar com seu telefone](../user-help/user-help-auth-app-sign-in.md)

A Microsoft fornece [modelos de comunicação](https://aka.ms/mfatemplates)MFA, [modelos de comunicação](https://www.microsoft.com/download/details.aspx?id=56768)de autoatendimento de redefinição de senha (SSPR) e [documentação do usuário final](../user-help/security-info-setup-signin.md) para ajudar a rascunhar suas comunicações. Você pode enviar usuários para [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) registrar-se diretamente, selecionando os links de informações de segurança nessa página.

### <a name="testing-passwordless"></a>Testando com senha

Em cada estágio de sua implantação, certifique-se de que você está testando que os resultados são os esperados.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testando o aplicativo Microsoft Authenticator

Veja a seguir exemplos de casos de teste para autenticação sem senha com o aplicativo Microsoft Authenticator

| Cenário | Resultados esperados |
| --- | --- |
| O usuário pode registrar Microsoft Authenticator aplicativo | O usuário pode registrar o aplicativo do aka.ms/mysecurityinfo |
| O usuário pode habilitar a entrada pelo telefone | Entrada pelo telefone configurada para a conta corporativa |
| O usuário pode acessar um aplicativo com entrada no telefone | O usuário passa pelo fluxo de entrada do telefone e atinge o aplicativo designado. |
| Testar o registro de entrada no telefone desligando Microsoft Authenticator entrada sem senha na tela métodos de autenticação no portal Azure Active Directory | Os usuários anteriormente habilitados não podem usar a entrada sem senha no Microsoft Authenticator. |
| Removendo a entrada pelo telefone do aplicativo Microsoft Authenticator | A conta de trabalho não está mais disponível no Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Testando chaves de segurança

Veja a seguir exemplos de casos de teste para autenticação sem senha com chaves de segurança.

**FIDO sem senha entre para Azure Active Directory dispositivos Windows 10 associados**

| Cenário | Resultados esperados |
| --- | --- |
| O usuário pode registrar o dispositivo FIDO2 (1809) | O usuário pode registrar o dispositivo FIDO2 usando as configurações > contas > opções de entrada > chave de segurança |
| O usuário pode redefinir o dispositivo FIDO2 (1809) | O usuário pode redefinir o dispositivo FIDO2 usando o software do fabricante |
| O usuário pode entrar com o dispositivo FIDO2 (1809) | O usuário pode selecionar a chave de segurança da janela de entrada e entrar com êxito. |
| O usuário pode registrar o dispositivo FIDO2 (1903) | O usuário pode registrar o dispositivo FIDO2 em Configurações > contas > opções de entrada > chave de segurança |
| O usuário pode redefinir o dispositivo FIDO2 (1903) | O usuário pode redefinir o dispositivo FIDO2 nas configurações > contas > opções de entrada > chave de segurança |
| O usuário pode entrar com o dispositivo FIDO2 (1903) | O usuário pode selecionar a chave de segurança da janela de entrada e entrar com êxito. |

**FIDO sem senha entre em aplicativos Web do Azure AD**

| Cenário | Resultados esperados |
| --- | --- |
| O usuário pode registrar o dispositivo FIDO2 em aka.ms/mysecurityinfo usando o Microsoft Edge | O registro deve ter sucesso |
| O usuário pode registrar o dispositivo FIDO2 em aka.ms/mysecurityinfo usando o Firefox | O registro deve ter sucesso |
| O usuário pode entrar no OneDrive online usando o dispositivo FIDO2 usando o Microsoft Edge | A entrada deve ter sucesso |
| O usuário pode entrar no OneDrive online usando o dispositivo FIDO2 usando o Firefox | A entrada deve ter sucesso |
| Teste a reversão do registro de dispositivo FIDO2 desativando as chaves de segurança FIDO2 na folha métodos de autenticação no portal Azure Active Directory | Os usuários serão solicitados a entrar usando sua chave de segurança, ele os registrará com êxito e um erro será exibido: "a política da empresa requer que você use um método diferente para entrar". Os usuários devem ser capazes de selecionar um método diferente e entrar com êxito. Feche a janela e entre novamente para verificar se elas não veem a mesma mensagem de erro. |

### <a name="auditing-passwordless"></a>Auditando com senha

O Azure AD tem relatórios que fornecem informações técnicas e de negócios. Seus proprietários de aplicativos técnicos e de negócios assumem a propriedade e consomem esses relatórios com base nos requisitos da sua organização.

A seção métodos de autenticação no portal de Azure Active Directory é onde os administradores podem habilitar e gerenciar configurações para credenciais com senha.

O Azure AD adiciona entradas aos logs de auditoria quando:

- Um administrador faz alterações na seção métodos de autenticação.
- Um usuário faz qualquer tipo de alteração em suas credenciais dentro de Azure Active Directory.

A tabela a seguir fornece alguns exemplos de cenários de relatório típicos.

|   | Faça a gestão do risco. | Aumentar a produtividade | Governação e conformidade |
| --- | --- | --- | --- |
| **Tipos de relatório** | Métodos de autenticação – usuários registrados para o registro de segurança combinado | Métodos de autenticação – usuários registrados para notificação de aplicativo | Entradas: revise quem está acessando o locatário e como |
| **Ações potenciais** | Usuários de destino ainda não registrados | Impulsionar a adoção de Microsoft Authenticator aplicativo ou chaves de segurança | Revogar o acesso ou impor políticas de segurança adicionais para administradores |

O **Azure ad retém a maioria dos dados de auditoria por 30 dias** e disponibiliza os dados por meio do portal de administração do Azure ou da API para que você faça o download em seus sistemas de análise. Se sua organização exigir maior retenção, os logs precisarão ser exportados e consumidos em uma ferramenta SIEM, como o [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk ou a lógica do resumo. [Saiba mais sobre como exibir seus relatórios de acesso e uso](../reports-monitoring/overview-reports.md).

Os usuários podem registrar e gerenciar suas credenciais navegando até [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Esse link direciona os usuários para a experiência de gerenciamento de credenciais do usuário final que foi habilitada por meio da experiência combinada de registro SSPR/MFA. Qualquer registro de dispositivos de segurança FIDO2 ou alterações nos métodos de autenticação por um usuário é registrado no Azure Active Directory logs de auditoria.

Quando os usuários habilitam ou desabilitam a conta em uma chave de segurança, ou redefinem o segundo fator para a chave de segurança em seus computadores com Windows 10, uma entrada é adicionada ao log de segurança e está sob as seguintes IDs de evento: 4670, 5382.

### <a name="plan-for-rollback"></a>Planejar a reversão

Embora a autenticação sem senha seja um recurso leve com impacto mínimo sobre os usuários finais, pode ser necessário reverter.

Reverter requer que o administrador entre no portal de Azure Active Directory, selecione os métodos de autenticação forte desejados e altere a opção Habilitar para ' não '. Esse processo desativará a funcionalidade de senha para todos os usuários.

Os usuários que já registraram dispositivos de segurança FIDO2 serão solicitados a usar o dispositivo de segurança na próxima entrada e, em seguida, verão o seguinte erro:

![Escolha uma maneira diferente de entrar](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

### <a name="plan-to-pilot"></a>Planejar para o piloto

Ao implantar a autenticação com senha, você deve primeiro habilitar um ou mais grupos pilotos. Você pode [criar grupos](../fundamentals/active-directory-groups-create-azure-portal.md) especificamente para essa finalidade. Adicione os usuários que farão parte do piloto para os grupos. Em seguida, habilite novos métodos de autenticação com senha para os grupos selecionados.

Os grupos podem ser sincronizados de um diretório local ou do Azure AD. Quando estiver satisfeito com os resultados do seu piloto, você poderá alternar a autenticação sem senha para todos os usuários.

Consulte [práticas recomendadas para um piloto](https://aka.ms/deploymentplans) na página planos de implantação.

## <a name="deploy-passwordless-authentication"></a>Implantar autenticação com senha

Siga as etapas alinhadas ao método escolhido abaixo.

### <a name="required-administrative-roles"></a>Funções administrativas necessárias

| Função do Azure AD | Descrição |
| --- | --- |
| Administrador de autenticação | Função menos privilegiada capaz de implementar e gerenciar métodos de autenticação |
| Utilizador | Função com privilégios mínimos para configurar o aplicativo autenticador no dispositivo ou para registrar o dispositivo de chave de segurança para entrada na Web ou no Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Implantar a entrada pelo telefone com o aplicativo Microsoft Authenticator

Siga as etapas no artigo habilitar a [entrada sem senha com o aplicativo Microsoft Authenticator](howto-authentication-passwordless-phone.md) para habilitar o aplicativo Microsoft Authenticator como um método de autenticação sem senha em sua organização.

### <a name="deploy-fido2-security-key-sign-in"></a>Implantar entrada de chave de segurança FIDO2

Siga as etapas no artigo habilitar a [entrada de chave de segurança sem senha para o Azure ad](howto-authentication-passwordless-security-key.md) para habilitar as chaves de segurança FIDO2 como métodos de autenticação sem senha em sua organização.

### <a name="troubleshoot-phone-sign-in"></a>Solucionar problemas de entrada no telefone

| Cenário | Solução |
| --- | --- |
| O usuário não pode executar o registro combinado | Verifique se o [registro combinado](concept-registration-mfa-sspr-combined.md) está habilitado. |
| O usuário não pode habilitar o aplicativo autenticador de entrada no telefone | Garantir que o usuário esteja no escopo para implantação |
| O usuário não está no escopo para autenticação sem senha, mas é apresentado com a opção de entrada sem senha, que não pode ser concluída. | Esse cenário ocorre quando o usuário habilitou a entrada no telefone n do aplicativo antes da criação da política. <br> Para habilitar a entrada: Adicione o usuário ao escopo de usuários habilitados para conexão sem senha. <br> Para bloquear a entrada: faça com que o usuário remova seu formulário de credencial desse aplicativo. |

### <a name="troubleshoot-security-key-sign-in"></a>Solucionar problemas de entrada de chave de segurança

| Cenário | Solução |
| --- | --- |
| O usuário não pode executar o registro combinado | Verifique se o [registro combinado](concept-registration-mfa-sspr-combined.md) está habilitado. |
| O usuário não pode adicionar uma chave de segurança em suas [configurações de segurança](https://aka.ms/mysecurityinfo) | Verifique se [as chaves de segurança](howto-authentication-passwordless-security-key.md) estão habilitadas. |
| O usuário não pode adicionar a chave de segurança nas opções de entrada do Windows 10 | [Verifique se as chaves de segurança para entrar no Windows](howto-authentication-passwordless-enable.md) |
| **Mensagem de erro**: detectamos que este navegador ou sistema operacional não oferece suporte a chaves de segurança FIDO2. | Os dispositivos de segurança FIDO2 sem senha só podem ser registrados em navegadores com suporte (Microsoft Edge, Firefox versão 67) no Windows 10 versão 1809 ou superior. |
| **Mensagem de erro**: a política da sua empresa requer que você use um método diferente para entrar. | Não se esqueça de que as chaves de segurança estão habilitadas no locatário. |
| O usuário não pode gerenciar minha chave de segurança no Windows 10 versão 1809 | A versão 1809 requer que você use o software de gerenciamento de chaves de segurança fornecido pelo fornecedor de chave FIDO2. Contate o fornecedor para obter suporte. |
| Acho que minha chave de segurança do FIDO2 pode estar com defeito — como posso testá-la | Navegue até [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), insira as credenciais para uma conta de teste, conecte a chave de segurança suspeita, clique no botão "+" na parte superior direita da tela, clique em criar e passe pelo processo de criação. Se esse cenário falhar, o dispositivo poderá estar com defeito. |

## <a name="next-steps"></a>Passos seguintes

- [Habilitar chaves de segurança sem senha para entrar no Azure AD](howto-authentication-passwordless-security-key.md)
- [Habilitar a entrada sem senha com o aplicativo Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Saiba mais sobre o uso de métodos de autenticação & insights](howto-authentication-methods-usage-insights.md)
