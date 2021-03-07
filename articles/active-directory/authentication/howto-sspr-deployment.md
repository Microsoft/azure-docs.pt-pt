---
title: Considerações de implantação para o Azure Ative Directory autosserviço de autosserviço reset
description: Conheça as considerações de implementação e estratégia para a implementação bem sucedida do reset da palavra-passe de autosserviço Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce8aa5108b6998424b35e3f4ecac6b51e78f276b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424126"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Planeie uma implementação de autosserviço do Azure Ative Directory

> [!IMPORTANT]
> Este plano de implementação oferece orientação e boas práticas para a implementação do reset da palavra-passe de autosserviço AZure AD (SSPR).
>
> **Se é um utilizador final e precisa de voltar [https://aka.ms/sspr](https://aka.ms/sspr) à sua conta, vá para**.

[Self-Service Password Reset (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) é uma funcionalidade do Azure Ative Directory (AD) que permite aos utilizadores redefinirem as suas palavras-passe sem contactarem o pessoal de TI para obterem ajuda. Os utilizadores podem desbloquear-se rapidamente e continuar a trabalhar independentemente de onde estejam ou a hora do dia. Ao permitir que os colaboradores se desbloqueiem, a sua organização pode reduzir o tempo não produtivo e os elevados custos de suporte para as questões mais comuns relacionadas com a palavra-passe.

A SSPR tem as seguintes capacidades-chave:

* O self-service permite que os utilizadores finais reiniciem as suas palavras-passe caducadas ou não caducadas sem contactarem um administrador ou ajudam a obter suporte.
* [O Writeback de Password](./concept-sspr-writeback.md) permite a gestão de senhas no local e a resolução do bloqueio de conta embora na nuvem.
* Os relatórios de atividade de gestão de passwords dão aos administradores informações sobre a reposição de passwords e a atividade de registo que ocorre na sua organização.

Este guia de implementação mostra-lhe como planear e, em seguida, testar um roll-out SSPR.

Para ver rapidamente a SSPR em ação e depois voltar a compreender considerações adicionais de implantação:

> [!div class="nextstepaction"]
> [Ativar o reset da palavra-passe de autosserviço (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Saiba mais sobre a SSPR

Saiba mais sobre a SSPR. Veja [como funciona: Azure AD autosserviço de autosserviço reset](./concept-sspr-howitworks.md).

### <a name="key-benefits"></a>Principais vantagens

Os principais benefícios de permitir a SSPR são:

* **Gerir o custo**. A SSPR reduz os custos de suporte de TI, permitindo que os utilizadores reiniciem as palavras-passe por si próprios. Também reduz o custo do tempo perdido devido a senhas perdidas e bloqueios. 

* **Experiência intuitiva do utilizador.** Fornece um processo intuitivo de registo de utilizadores único que permite aos utilizadores redefinir palavras-passe e desbloquear contas a pedido de qualquer dispositivo ou localização. A SSPR permite que os utilizadores voltem a trabalhar mais rapidamente e sejam mais produtivos.

* **Flexibilidade e segurança.** A SSPR permite às empresas aceder à segurança e flexibilidade que uma plataforma de nuvem proporciona. Os administradores podem alterar as definições para acomodar novos requisitos de segurança e lançar estas alterações para os utilizadores sem perturbar a sua inscrição.

* **Auditoria robusta e rastreio de utilização.** Uma organização pode garantir que os sistemas de negócio se mantenham seguros enquanto os seus utilizadores reiniciam as suas próprias palavras-passe. Os registos de auditoria robustos incluem informações de cada passo do processo de reset da palavra-passe. Estes registos estão disponíveis a partir de uma API e permitem ao utilizador importar os dados para um sistema de monitorização de incidentes de segurança e monitorização de eventos (SIEM).

### <a name="licensing"></a>Licensing

O Azure Ative Directory é licenciado por utilizador, o que significa que cada utilizador necessita de uma licença adequada para as funcionalidades que utiliza. Recomendamos licenças baseadas em grupo para a SSPR. 

Para comparar edições e funcionalidades e permitir o licenciamento em grupo ou no utilizador, consulte os requisitos de [licenciamento para o reset da palavra-passe de autosserviço AD Azure](./concept-sspr-licensing.md).

Para obter mais informações sobre preços, consulte [os preços do Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Pré-requisitos

* Um inquilino do Azure AD em funcionamento com, pelo menos, uma licença de avaliação ativada. Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Uma conta com privilégios de Administrador Global.


### <a name="training-resources"></a>Recursos de formação

| Recursos| Link e Descrição |
| - | - |
| Vídeos| [Capacitar os seus utilizadores com uma melhor escalabilidade de TI](https://youtu.be/g9RpRnylxS8) 
| |[O que é a reposição personalizada de palavra-passe?](https://youtu.be/hc97Yx5PJiM)|
| |[Implantação de autosserviço de senha reset](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Como ativar e configurar a SSPR em Azure AD](https://www.youtube.com/watch?v=rA8TvhNcCvQ)|
| |[Como configurar a palavra-passe de autosserviço para os utilizadores em Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Como [preparar os utilizadores] para registar [as suas] informações de segurança para o Azure Ative Directory](https://youtu.be/gXuh0XS18wA) |
| Cursos online|[Gestão de Identidades no Microsoft Azure Ative Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Utilize a SSPR para dar aos seus utilizadores uma experiência moderna e protegida. Consulte especialmente o módulo "[Managing Azure Ative Directory Users and Groups](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)". |
|Cursos pagos pluralsight |[As Questões da Gestão da Identidade e do Acesso](https://www.pluralsight.com/courses/identity-access-management-issues) Conheça o IAM e questões de segurança para estar atento na sua organização. Consulte especialmente o módulo "Outros Métodos de Autenticação".|
| |[Começar com a Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Aprenda as melhores práticas para estender os ativos no local à nuvem de uma forma que permita a autenticação, autorização, encriptação e uma experiência móvel segura. Consulte especialmente o módulo "Configurar funcionalidades avançadas do Microsoft Azure Ative Directory Premium".
|Tutoriais |[Complete um lançamento de autosserviço de autosserviço AD Azure](./tutorial-enable-sspr.md) |
| |[Ativar o writeback da palavra-passe](./tutorial-enable-sspr-writeback.md) |
| |[Palavra-passe AD do Azure reiniciada a partir do ecrã de login do Windows 10](./howto-sspr-windows.md) |
| FAQ|[Gestão de passwords frequentemente perguntas](./active-directory-passwords-faq.md) |


### <a name="solution-architecture"></a>Arquitetura de soluções

O exemplo a seguir descreve a arquitetura da solução de reposição de palavras-passe para ambientes híbridos comuns.

![diagrama de arquitetura de solução](./media/howto-sspr-deployment//solutions-architecture.png)

Descrição do fluxo de trabalho

Para redefinir a palavra-passe, os utilizadores vão ao portal de reset da [palavra-passe](https://aka.ms/sspr). Devem verificar o método ou métodos de autenticação previamente registados para comprovar a sua identidade. Se redefinirem a palavra-passe com sucesso, iniciam o processo de reset.

* Para utilizadores apenas na nuvem, a SSPR armazena a nova palavra-passe no Azure AD. 

* Para os utilizadores híbridos, a SSPR rescreve a palavra-passe para o Diretório Ativo on-prem através do serviço Azure AD Connect. 

Nota: Para utilizadores que tenham [a sincronização de hash password (PHS)](../hybrid/whatis-phs.md) desativada, a SSPR armazena as palavras-passe apenas no Diretório Ativo on-prem.

### <a name="best-practices"></a>Melhores práticas

Pode ajudar os utilizadores a registarem-se rapidamente, implementando o SSPR ao lado de outra aplicação ou serviço popular na organização. Esta ação gerará um grande volume de inscrições e impulsionará o registo.

Antes de implementar o SSPR, pode optar por determinar o número e o custo médio de cada chamada de reset de palavra-passe. Pode utilizar esta implementação de posts de dados para mostrar o valor que a SSPR está a trazer para a organização.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Permitir o registo combinado de SSPR e MFA

A Microsoft recomenda que as organizações permitam a experiência de registo combinado para a autenticação de SSPR e multi-factor. Quando ativa esta experiência de registo combinado, os utilizadores só precisam de selecionar as suas informações de registo uma vez para ativar ambas as funcionalidades.

A experiência de registo combinado não requer que as organizações permitam a autenticação multi-factor SSPR e Azure AD. O registo combinado proporciona às organizações uma melhor experiência de utilização. Para mais informações, consulte [o registo combinado de informações de segurança](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Planear o projeto de implantação

Considere as suas necessidades organizacionais enquanto determina a estratégia para esta implantação no seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, normalmente fazem-no devido a expectativas desajustadas no impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas](../fundamentals/active-directory-deployment-plans.md) e que as partes interessadas no projeto são bem compreendidas documentando as partes interessadas e os seus contributos e contas do projeto.

#### <a name="required-administrator-roles"></a>Funções de administrador exigidas


| Papel de Negócio/Persona| Papel da AD Azure (se necessário) |
| - | - |
| Helpdesk de nível 1| Administrador de palavras-passe |
| Helpdesk de nível 2| Administrador de utilizadores |
| Administrador da SSPR| Administrador global |


### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Deve comunicar proativamente com os seus utilizadores como a sua experiência vai mudar, quando vai mudar, e como obter apoio se eles experimentarem problemas. Reveja os [materiais de lançamento de autosserviço no centro de descarregamento da Microsoft](https://www.microsoft.com/download/details.aspx?id=56768) para obter ideias sobre como planear a sua estratégia de comunicação do utilizador final.

### <a name="plan-a-pilot"></a>Planeie um piloto

Recomendamos que a configuração inicial de SSPR se encontra num ambiente de teste. Comece com um grupo piloto, permitindo à SSPR um subconjunto de utilizadores na sua organização. Consulte [as melhores práticas para um piloto.](../fundamentals/active-directory-deployment-plans.md)

Para criar um grupo, consulte como [criar um grupo e adicionar membros no Azure Ative Directory](../fundamentals/active-directory-groups-create-azure-portal.md). 

## <a name="plan-configuration"></a>Configuração do plano

São necessárias as seguintes definições para ativar o SSPR juntamente com os valores recomendados.

| Área | Definição | Valor |
| --- | --- | --- |
| **Propriedades SSPR** | Redefinição da palavra-passe de autosserviço ativada | **Grupo selecionado** para piloto / **Todos** para produção |
| **Métodos de autenticação** | Métodos de autenticação necessários para registar | Sempre 1 mais do que o necessário para reset |
|   | Métodos de autenticação necessários para repor | Um ou dois |
| **Inscrição** | Exigir que os utilizadores se registem ao iniciar sessão | Sim |
|   | Número de dias antes de ser pedido aos utilizadores que voltem a confirmar as informações de autenticação | 90 - 180 dias |
| **Notificações** | Notificar os utilizadores sobre reposições de palavras-passe | Sim |
|   | Notificar todos os administradores quando outros administradores repõem as palavras-passe deles | Sim |
| **Personalização** | Personalizar link helpdesk | Sim |
|   | E-mail de ajuda personalizada ou URL | Site de suporte ou endereço de e-mail |
| **Integração no local** | Descreva palavras-passe para a AD no local | Sim |
|   | Permitir que os utilizadores desbloqueiem a conta sem redefinir a palavra-passe | Sim |

### <a name="sspr-properties"></a>Propriedades SSPR

Ao ativar a SSPR, escolha um grupo de segurança adequado no ambiente piloto.

* Para impor o registo SSPR para todos, recomendamos a utilização da opção **All.**
* Caso contrário, selecione o grupo de segurança Azure AD ou AD apropriado.

### <a name="authentication-methods"></a>Métodos de autenticação

Quando o SSPR está ativado, os utilizadores só podem redefinir a sua palavra-passe se tiverem dados presentes nos métodos de autenticação que o administrador ativou. Os métodos incluem telefone, notificação de aplicação autenticadora, questões de segurança, etc. Para mais informações, consulte [o que são métodos de autenticação?](./concept-authentication-methods.md)

Recomendamos as seguintes definições do método de autenticação:

* Descreva os **métodos de autenticação necessários para registar-se** para pelo menos mais um do que o número necessário para reiniciar. Permitir múltiplas autenticações dá flexibilidade aos utilizadores quando precisam de ser reiniciados.

* **Desajuste número de métodos necessários para reiniciar** para um nível adequado à sua organização. Um requer menos atrito, enquanto dois podem aumentar a sua postura de segurança. 

Nota: O utilizador deve ter os métodos de autenticação configurados nas políticas e restrições de [Password no Diretório Ativo Azure](./concept-sspr-policy.md).

### <a name="registration-settings"></a>Definições de registo

Definir **Exigir que os utilizadores se registem** ao iniciar sessão no **Sim**. Esta definição requer que os utilizadores se registem ao iniciar sessão, garantindo que todos os utilizadores estão protegidos.

**Desajuste o número de dias antes de os utilizadores serem convidados a reconfirmar as suas informações de autenticação** entre **90** e **180** dias, a menos que a sua organização tenha necessidade de um prazo mais curto.

### <a name="notifications-settings"></a>Definições de notificações

Configure tanto os **utilizadores notificadores em redefinições de palavra-passe** como os **administradores notificando todos os administradores quando outros administradores redefinirem a sua palavra-passe** para **Sim**. Selecionar **Sim** em ambos aumenta a segurança, garantindo que os utilizadores estão cientes quando a sua palavra-passe é reiniciada. Também garante que todos os administradores estão cientes quando um administrador muda uma palavra-passe. Se os utilizadores ou administradores receberem uma notificação e não iniciarem a alteração, podem reportar imediatamente um problema de segurança potencial.

### <a name="customization-settings"></a>Definições de personalização

É fundamental personalizar o e-mail ou URL de helpdesk para garantir que os utilizadores que experimentam problemas podem obter ajuda imediatamente. Desaça esta opção para um endereço de e-mail ou página web comum que os seus utilizadores conhecem. 

Para obter mais informações, consulte [Personalizar a funcionalidade AD Azure para reiniciar a palavra-passe de autosserviço](./howto-sspr-customization.md).

### <a name="password-writeback"></a>Writeback de palavra-passe

**O Writeback de palavra-passe** é ativado com [o Azure AD Connect](../hybrid/whatis-hybrid-identity.md) e escreve resets de palavra-passe na nuvem de volta para um diretório existente no local em tempo real. Para mais informações, consulte [o que é o Writeback da Palavra-Passe?](./concept-sspr-writeback.md)

Recomendamos as seguintes definições:

* Certifique-se de que **a gravação de palavras-passe para a AD no local** está definida para **Sim**. 
* Desajuste a **Conta Permitir que os utilizadores desbloqueiem a conta sem redefinir a palavra-passe** para **Sim**.

Por padrão, o Azure AD desbloqueia as contas quando executa uma redefinição de palavra-passe.

### <a name="administrator-password-setting"></a>Definição de senha de administrador

As contas do administrador têm permissões elevadas. A empresa no local ou os administradores de domínio não podem redefinir as suas palavras-passe através da SSPR. As contas de administração no local têm as seguintes restrições:

* só pode alterar a sua palavra-passe no seu ambiente pré-pré-dia.
* nunca pode usar as perguntas e respostas secretas como um método para redefinir a sua palavra-passe.

Recomendamos que não sincronize as suas contas de administração do Ative Directory com a Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambientes com múltiplos sistemas de gestão de identidade

Alguns ambientes têm múltiplos sistemas de gestão de identidade. Gestores de identidade no local, como a Oracle AM e o SiteMinder, necessitam de sincronização com AD para senhas. Pode fazê-lo utilizando uma ferramenta como o Serviço de Notificação de Alteração de Palavras-Passe (PCNS) com o Microsoft Identity Manager (MIM). Para encontrar informações sobre este cenário mais complexo, consulte o artigo [Implementar o Serviço de Notificação de Alteração de Palavras-Passe MIM num controlador de domínio](/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Teste e suporte de planos

Em cada fase da sua implantação de grupos piloto iniciais através de toda a organização, certifique-se de que os resultados são esperados.

### <a name="plan-testing"></a>Testes de plano

Para garantir que a sua implantação funcione como esperado, planeie um conjunto de casos de teste para validar a implementação. Para avaliar os casos de teste, precisa de um utilizador de teste não administrador com uma palavra-passe. Se precisar de criar um utilizador, consulte [adicionar novos utilizadores ao Azure Ative Directory](../fundamentals/add-users-azure-active-directory.md).

A tabela que se segue inclui cenários de teste úteis que pode utilizar para documentar os resultados esperados das suas organizações com base nas suas políticas.
<br>


| Caso de negócios| Resultados esperados |
| - | - |
| Portal SSPR é acessível a partir da rede corporativa| Determinado pela sua organização |
| Portal SSPR é acessível de fora da rede corporativa| Determinado pela sua organização |
| Redefinir a palavra-passe do utilizador do navegador quando o utilizador não estiver ativado para reposição de password| O utilizador não consegue aceder ao fluxo de reset da palavra-passe |
| Redefinir a palavra-passe do utilizador do navegador quando o utilizador não tiver registado o reset da palavra-passe| O utilizador não consegue aceder ao fluxo de reset da palavra-passe |
| O utilizador assina quando é aplicado para fazer o registo de reset de senha| Solicita ao utilizador que registe informações de segurança |
| O utilizador assina quando o registo de reset da palavra-passe está completo| Solicita ao utilizador que registe informações de segurança |
| Portal SSPR é acessível quando o utilizador não tem licença| É acessível |
| Redefinir a palavra-passe do utilizador do Windows 10 AZure AD junta-se ou híbrido Azure AD ad| O utilizador pode redefinir a palavra-passe |
| Os dados de registo e utilização da SSPR estão disponíveis para os administradores em tempo real| Está disponível através de registos de auditoria |

Também pode consultar completar [um rolo de piloto de autosserviço AD Azure.](./tutorial-enable-sspr.md) Neste tutorial, você ativará um lançamento piloto da SSPR na sua organização e testará usando uma conta não administrador.

### <a name="plan-support"></a>Apoio ao plano

Embora a SSPR não crie normalmente problemas de utilização, é importante preparar pessoal de apoio para lidar com problemas que possam surgir. Embora um administrador possa redefinir a palavra-passe para utilizadores finais através do portal AD Azure, é melhor ajudar a resolver o problema através de um processo de suporte de autosserviço.

Para ativar o sucesso da sua equipa de suporte, pode criar um FAQ baseado em questões que recebe dos seus utilizadores. Eis alguns exemplos:

| Cenários| Descrição |
| - | - |
| O utilizador não dispõe de quaisquer métodos de autenticação registados| Um utilizador está a tentar redefinir a sua palavra-passe mas não dispõe de nenhum dos métodos de autenticação que registou disponíveis (Exemplo: deixaram o telemóvel em casa e não conseguem aceder ao e-mail) |
| O utilizador não está a receber uma mensagem ou uma chamada no seu escritório ou telemóvel| Um utilizador está a tentar verificar a sua identidade por texto ou chamada, mas não está a receber um texto/chamada. |
| O utilizador não pode aceder ao portal de reset da palavra-passe| Um utilizador quer redefinir a sua palavra-passe mas não está habilitado para o reset da palavra-passe e não pode aceder à página para atualizar palavras-passe. |
| O utilizador não pode definir uma nova senha| Um utilizador completa a verificação durante o fluxo de reset da palavra-passe, mas não consegue definir uma nova palavra-passe. |
| O utilizador não vê uma ligação de palavra-passe reset num dispositivo Windows 10| Um utilizador está a tentar redefinir a palavra-passe a partir do ecrã de bloqueio do Windows 10, mas o dispositivo não está associado ao Azure AD, ou a política do dispositivo Intune não está ativada. |

### <a name="plan-rollback"></a>Plano de reversão

Para reverter a implantação:

* para um único utilizador, remova o utilizador do grupo de segurança 

* para um grupo, remova o grupo da configuração SSPR

* Para todos, desative a SSPR para o inquilino da AD Azure

## <a name="deploy-sspr"></a>Implementar a SSPR

Antes de partir, certifique-se de que fez o seguinte:

1. Criado e iniciado a executar o seu plano de [comunicação.](#plan-communications)

1. Determinou as definições de [configuração](#plan-configuration)adequadas .

1. Identificou os utilizadores e grupos para os ambientes [piloto](#plan-a-pilot) e de produção.

1. [Definições de configuração determinadas](#plan-configuration) para registo e self-service.

1. [Writeback de palavra-passe configurado](#password-writeback) se tiver um ambiente híbrido.


**Estão agora prontos para lançar a SSPR!**

Consulte [o reset da palavra-passe de autosserviço](./tutorial-enable-sspr.md#enable-self-service-password-reset) para obter instruções completas passo a passo na configuração das seguintes áreas.

1. [Métodos de autenticação](./concept-authentication-methods.md)

1. [Definições de registo](./concept-registration-mfa-sspr-combined.md)

1. [Definições de notificações](#notifications-settings)

1. [Definições de personalização](./howto-sspr-customization.md)

1. [Integração no local](./tutorial-enable-sspr-writeback.md)

### <a name="enable-sspr-in-windows"></a>Ativar SSPR no Windows
Para máquinas que executam o Windows 7, 8, 8.1 e 10, pode [permitir que os utilizadores reiniciem a sua palavra-passe no ecrã do Windows](./howto-sspr-windows.md)

## <a name="manage-sspr"></a>Gerir a SSPR

A Azure AD pode fornecer informações adicionais sobre o seu desempenho SSPR através de auditorias e relatórios.

### <a name="password-management-activity-reports"></a>Relatórios de atividade de gestão de passwords 

Pode utilizar relatórios pré-construídos no portal Azure para medir o desempenho da SSPR. Se você estiver devidamente licenciado, também pode criar consultas personalizadas. Para obter mais informações, consulte [opções de reporte para a gestão de passwords Azure AD](./howto-sspr-reporting.md)

> [!NOTE]
>  Você deve ser [um administrador global](../roles/permissions-reference.md), e você deve optar por estes dados para ser recolhido para a sua organização. Para optar, deve visitar o separador Reportar ou os registos de auditoria no Portal Azure pelo menos uma vez. Até lá, os dados não são recolhidos para a sua organização.

Os registos de auditoria para o registo e o reset da palavra-passe estão disponíveis durante 30 dias. Se a auditoria de segurança dentro da sua corporação necessitar de uma retenção mais longa, os registos precisam de ser exportados e consumidos numa ferramenta SIEM como [Azure Sentinel,](../../sentinel/connect-azure-active-directory.md)Splunk ou ArcSight.

![SSPR Reporting screenshot](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Métodos de autenticação - Utilização e Insights

[O uso e os insights](./howto-authentication-methods-usage-insights.md) permitem-lhe compreender como os métodos de autenticação para funcionalidades como Azure AD MFA e SSPR estão a funcionar na sua organização. Esta capacidade de reporte fornece à sua organização os meios para entender que métodos se registam e como usá-los.

### <a name="troubleshoot"></a>Resolução de problemas

* Consulte o [reset da palavra-passe de autosserviço de resolução de problemas](./troubleshoot-sspr.md) 

* Siga [a gestão de passwords com frequência](./active-directory-passwords-faq.md) 

### <a name="helpful-documentation"></a>Documentação útil

* [O que são métodos de autenticação?](./concept-authentication-methods.md)

* [Como funciona: Azure AD autosserviço palavra-passe reposta?](./concept-sspr-howitworks.md)

* [Personalize a funcionalidade AZURE AD para reset de senha de autosserviço](./howto-sspr-customization.md)

* [Password policies and restrictions in Azure Active Directory](./concept-sspr-policy.md) (Políticas de palavras-passe e restrições no Azure Active Directory)

* [O que é o Writeback da Palavra-Passe?](./concept-sspr-writeback.md)

## <a name="next-steps"></a>Passos seguintes

* Para começar a implementar sSPR, consulte o reset da [palavra-passe de autosserviço do Enable Azure AD](tutorial-enable-sspr.md)

* [Considere implementar a proteção de senha Azure AD](./concept-password-ban-bad.md)

* [Considere implementar o Azure AD Smart Lockout](./howto-password-smart-lockout.md)
