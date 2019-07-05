---
title: Plano de implementação - Azure Active Directory de reposição de palavra-passe self-service
description: Repor a estratégia de implementação bem-sucedida de palavra-passe self-service do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b566bfc3f6c49f6cb9fe31f166356f6ae351e38
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440957"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Implemente no Azure de reposição de palavras-passe self-service de AD

Reposição de palavra-passe self-service (SSPR) é uma funcionalidade do Azure Active Directory que permite que os funcionários reponham as palavras-passe sem a necessidade de contactar o departamento de TI. Os funcionários devem se registrar para ou ser registados para o self-service reposição palavra-passe antes de utilizar o serviço. Durante o registo, o funcionário escolhe um ou mais métodos de autenticação ativados pela sua organização.

SSPR permite que os funcionários rapidamente obter desbloqueado e continuar a trabalhar, independentemente de onde eles estão ou a hora do dia. Ao permitir que os utilizadores desbloquear a próprios, a sua organização pode reduzir o tempo não produtivos e os custos de suporte de elevada para problemas mais comuns relacionados com a palavra-passe.

Ajude os utilizadores que registe rapidamente ao implementar a SSPR juntamente com outro aplicativo ou serviço na sua organização. Esta ação irá gerar um grande volume de inícios de sessão e irá orientar o registo.

Antes de implementar a SSPR, as organizações podem desejar determinar quantos de redefinição de senha chamadas de suporte técnico relacionados acontecer ao longo do tempo e o custo médio de cada chamada. Podem utilizar esta implementação de publicação de dados para mostrar o valor que SSPR é trazer para sua organização.  

## <a name="how-sspr-works"></a>Como funciona a SSPR

1. Quando um utilizador tenta repor uma palavra-passe, eles tem de verificar seu método de autenticação registrado anteriormente ou métodos para provar a sua identidade.
1. Em seguida, o usuário insere uma nova palavra-passe.
   1. Para os utilizadores apenas na cloud, a nova palavra-passe é armazenado no Azure Active Directory. Para obter mais informações, consulte o artigo [como o SSPR funciona](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. Para utilizadores híbridos, a palavra-passe é gravada para o diretório de Active Directory no local através do serviço do Azure AD Connect. Para obter mais informações, consulte o artigo [o que é a repetição de escrita de palavra-passe](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Considerações sobre o licenciamento

O Azure Active Directory é o significado de por utilizador de licença que cada utilizador tem de ter uma licença adequada para os recursos que utilizem.

- Self-service reposição palavra-passe para utilizadores apenas na cloud está disponível com o Azure AD básico ou superior.
- Self-service reposição palavra-passe com repetição de escrita no local para ambientes híbridos requer o Azure AD Premium P1 ou superior.

Podem encontrar mais informações sobre o licenciamento no [Azure Active Directory, página de preços](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Ativar o registo combinado para SSPR e o MFA

A Microsoft recomenda que as organizações ativar a experiência de registo combinado para SSPR e autenticação multifator. Quando ativa esta experiência de registo combinado, os utilizadores tem de selecionar apenas uma vez suas informações de registo para permitir que os dois recursos.

![Registo de informações de segurança combinada](./media/howto-sspr-deployment/combined-security-info.png)

A experiência de registo combinado não requer que as organizações ativar SSPR e multi-factor Authentication utilizar. A experiência de registo combinado fornece às organizações uma melhor experiência de utilizador em comparação com os componentes individuais tradicionais. Obter mais informações sobre o registo combinado e como ativar, podem ser encontradas no artigo [combinado de registo de informações de segurança (pré-visualização)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>Planear a configuração

As seguintes definições são necessárias para ativar a SSPR, juntamente com os valores recomendados.

| Área | Definição | Value |
| --- | --- | --- |
| **Propriedades SSPR** | Reposição de palavra-passe self-service ativada | **Selecionou** grupo para piloto / **todos os** para produção |
| **Métodos de autenticação** | Métodos de autenticação necessários para o registo | Mais de 1 sempre que o necessário para a reposição |
|   | Métodos de autenticação necessários para a reposição | Um ou dois |
| **Registo** | Exigir que os utilizadores se registem ao iniciar sessão | Sim |
|   | Número de dias antes de ser pedido aos utilizadores que reconfirmem as informações de autenticação | 90, 180 dias |
| **Notificações** | Notificar os utilizadores sobre reposições de palavras-passe | Sim |
|   | Notificar todos os administradores quando outros administradores repõem as palavras-passe deles | Sim |
| **Personalização** | Personalizar ligação de suporte técnico | Sim |
|   | URL ou e-mail de suporte técnico personalizado | Endereço de e-mail ou site de suporte |
| **Integração no local** | Gravar palavras-passe de volta para o local AD | Sim |
|   | Permitir que os utilizadores desbloquear a conta sem repor a palavra-passe | Sim |

### <a name="sspr-properties-recommendations"></a>Recomendações de propriedades SSPR

Ao ativar a reposição de palavra-passe self-service, escolha um grupo de segurança a serem usados durante o piloto.

Ao planear iniciar o serviço mais amplamente, recomendamos que utilize a opção All para impor a SSPR para todas as pessoas na organização. Se não pode definir para todos, selecionar o Azure AD apropriado grupo de segurança ou grupo do AD sincronizado com o Azure AD.

### <a name="authentication-methods"></a>Métodos de autenticação

Defina métodos de autenticação necessários para o registo a, pelo menos, um mais do que o número necessário para a reposição. Permitir que vários dá aos usuários flexibilidade quando precisar de repor.

Definir **número de métodos necessários para repor** para um nível adequado à sua organização. Um requer o atrito menor, enquanto dois pode aumentar a sua postura de segurança.

Ver [quais são os métodos de autenticação](concept-authentication-methods.md) para obter informações detalhadas sobre qual das autenticações métodos estão disponíveis para SSPR, perguntas de segurança predefinidas e como criar perguntas de segurança personalizada.

### <a name="registration-settings"></a>Definições de registo

Definir **exigir que os utilizadores se registem ao iniciar sessão** ao **Sim**. Esta definição significa que os utilizadores são obrigados a se registem ao iniciar sessão, assegurando que todos os utilizadores são protegidos.

Definir **número de dias antes de ser pedido aos utilizadores que reconfirmem as informações de autenticação** para entre **90** e **180** dias, a menos que a sua organização tiver um negócio necessidade para um período de tempo mais curto.

### <a name="notifications-settings"></a>Definições de notificações

Configurar ambos os **notificar os utilizadores sobre reposições de palavra-passe** e o **notificar todos os administradores quando outros administradores repõem as palavras-passe** para **Sim**. Selecionando **Sim** em ambos aumenta a segurança ao garantir que os utilizadores estejam cientes quando a palavra-passe foi reposta e que todos os administradores têm em consideração quando um administrador altera uma palavra-passe. Se os utilizadores ou os administradores recebem uma notificação desse tipo, e não iniciou a alteração, pode comunicam imediatamente uma potencial violação de segurança.

### <a name="customization"></a>Personalização

É fundamental para personalizar o **suporte técnico URL ou e-mail** para garantir que os utilizadores que têm problemas rapidamente podem obter ajuda. Defina esta opção para um endereço de e-mail de suporte técnico comum ou página da web que os utilizadores estão familiarizados.

### <a name="on-premises-integration"></a>Integração no local

Se tiver um ambiente híbrido, certifique-se de que **gravar palavras-passe de volta para o local AD** está definida como **Sim**. Defina também a permitir aos utilizadores desbloquear sem repor a palavra-passe Yes, a conta dá mais flexibilidade.

### <a name="changingresetting-passwords-of-administrators"></a>Palavras-passe de alterar/Resetting de administradores

As contas de administrador são contas especiais com permissões elevadas. Para os proteger, as seguintes restrições aplicam-se para alterar as palavras-passe de administradores:

- Administradores da empresa no local ou administradores de domínio não é possível repor a palavra-passe através da SSPR. Apenas podem alterar a palavra-passe no respetivo ambiente no local. Assim, recomendamos que não sincronização no local Contas de administrador do AD para o Azure AD.
- Um administrador não pode utilizar secreta perguntas e respostas como um método para repor a palavra-passe.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambientes com vários sistemas de gestão de identidade

Se existirem várias identidades sistemas de gestão num ambiente, tais como gestores de identidade no local como a Oracle AM, SiteMinder, ou outros sistemas, em seguida, as palavras-passe escritas para o Active Directory, poderão ter de ser sincronizados com os outros sistemas usando uma ferramenta como a Password Change Notification Service (PCNS) com o Microsoft Identity Manager (MIM). Para obter informações sobre este cenário mais complexa, consulte o artigo [implementar o serviço de notificação de alteração à palavra-passe de MIM num controlador de domínio](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>Planear a implementação e suporte para SSPR

### <a name="engage-the-right-stakeholders"></a>Interaja com os participantes certos

Quando a ativação projetos tecnológicos, eles costumam fazer isso devido a expetativas sem correspondência em impacto, resultados e responsabilidades. Para evitar essas armadilhas, certifique-se de que estão participando os participantes certos, e que funções das partes interessadas no projeto estão bem compreendida por documentar as partes interessadas e sua entrada de projeto e a responsabilidade.

### <a name="communications-plan"></a>Plano de comunicações

A comunicação é crítica para o sucesso de qualquer serviço novo. Proativamente a comunicar com os seus utilizadores como utilizar o serviço e o que eles podem fazer para obter ajuda se algo não funcionar conforme esperado. Reveja os [materiais de implementação no Centro de transferências da Microsoft de reposição de palavra-passe self-service](https://www.microsoft.com/download/details.aspx?id=56768) para obter ideias sobre como planear a sua estratégia de comunicação do utilizador final.

### <a name="testing-plan"></a>Plano de testes

Para garantir que sua implantação funciona conforme esperado, deve planejar um conjunto de casos de teste, que utilizará para validar a implementação. A tabela seguinte inclui alguns cenários de teste útil que pode utilizar para suas organizações esperado resultados com base em suas diretivas de documento.

| Caso prático | Resultado esperado |
| --- | --- |
| Portal SSPR é acessível a partir da rede empresarial | Determinada pela sua organização |
| Portal SSPR é acessível a partir de fora da rede empresarial | Determinada pela sua organização |
| Repor palavra-passe de utilizador do browser quando o utilizador não está ativado para a reposição de palavra-passe | Utilizador não é capaz de acessar o fluxo de reposição de palavra-passe |
| Repor palavra-passe de utilizador do browser quando o utilizador não registado para a reposição de palavra-passe | Utilizador não é capaz de acessar o fluxo de reposição de palavra-passe |
| Utilizador inicia sessão no registo de reposição de palavra-passe de quando é imposta | É pedido ao utilizador para registar informações de segurança |
| Utilizador inicia sessão no registo de reposição de palavra-passe de quando foi concluída | Utilizador não deve registar informações de segurança |
| Portal SSPR é acessível quando o utilizador não tem uma licença | Está acessível |
| Repor palavra-passe de utilizador do Windows 10 AADJ ou H + AADJ ecrã de bloqueio do dispositivo depois do utilizador registou | Utilizador pode repor a palavra-passe |
| Dados de registo e a utilização SSPR estão disponíveis para os administradores em tempo real | Está disponível através de registos de auditoria |

### <a name="support-plan"></a>Plano de suporte

Embora SSPR, normalmente, não crie problemas do utilizador, é importante ter suporte equipe preparados para lidar com problemas que podem surgir.

Embora um administrador pode alterar ou repor a palavra-passe para os utilizadores finais através do portal do Azure AD, é melhor ajudar a resolver o problema através de um processo de suporte de gestão personalizada.

Na seção guia operacional deste documento, criar uma lista de incidentes de suporte e suas causas prováveis e criar um guia para a resolução.

### <a name="auditing-and-reporting"></a>Auditoria e relatórios

Após a implementação, muitas organizações querem saber como realmente está a ser utilizado ou se de reposição de palavra-passe self-service (SSPR). A funcionalidade de relatórios do Azure Active Directory (Azure AD) fornece ajuda a responder a perguntas através de relatórios criados previamente.

Registos de auditoria para a reposição de palavra-passe e de registo estão disponíveis durante 30 dias. Por conseguinte, se a auditoria de segurança dentro de uma empresa necessitar de retenção mais longa, os registos devem ser exportados e consumido, tal como numa ferramenta SIEM [Azure sentinela](../../sentinel/connect-azure-active-directory.md), Splunk e cso.

Numa tabela, como a mostrada abaixo, documente a agenda de cópia de segurança, o sistema e as partes responsáveis. Talvez não seja necessário separar as cópias de segurança de relatórios de auditoria e, mas deve ter uma cópia de segurança separada a partir da qual pode recuperar a partir de um problema.

|   | Frequência de transferência | Sistema de destino | Terceiros responsável |
| --- | --- | --- | --- |
| Cópia de segurança de auditoria |   |   |   |
| Cópia de segurança de relatórios |   |   |   |
| Cópia de segurança de recuperação de desastres |   |   |   |

## <a name="implementation"></a>Implementação

Implementação ocorre em três fases:

- Configurar utilizadores e licenças
- Configurar o Azure AD SSPR para o registo e Self-Service
- Configurar o Azure AD Connect para repetição de escrita de palavra-passe

### <a name="communicate-the-change"></a>Comunicar a alteração

Começa a implementação do plano de comunicações que desenvolvidos na fase de planejamento.

### <a name="ensure-groups-are-created-and-populated"></a>Certifique-se de que grupos são criados e preenchidos

A secção de métodos de autenticação de palavra-passe de planejamento de referência e certifique-se de que o grupo (s) para a implementação piloto ou de produção está disponíveis e todos os utilizadores apropriados são adicionados aos grupos.

### <a name="apply-licenses"></a>Aplicar as licenças

Os grupos a que se pretender implementar tem de ter o Azure AD licença premium atribuída. Pode atribuir licenças diretamente para o grupo ou pode utilizar políticas licença existente, tal como através do PowerShell ou o licenciamento baseado no grupo.

Informações sobre a atribuição de licenças em grupos de utilizadores podem ser encontradas no artigo [atribuir licenças aos utilizadores pela associação de grupo no Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Configurar a SSPR

#### <a name="enable-groups-for-sspr"></a>Ativar grupos para SSPR

1. Aceda ao portal do Azure com uma conta de administrador.
1. Selecione todos os serviços e na caixa de filtro, escreva o Azure Active Directory e, em seguida, selecione Azure Active Directory.
1. No painel do Active Directory, selecione a reposição de palavra-passe.
1. No painel de propriedades, selecione selecionados. Se pretender que todos os utilizadores ativados, Selecionar tudo.
1. A palavra-passe predefinido painel de política de reposição de, escreva o nome do primeiro grupo, selecioná-la e, em seguida, clique em selecionar na parte inferior do ecrã e selecione guardar na parte superior do ecrã.
1. Repita este processo para cada grupo.

#### <a name="configure-the-authentication-methods"></a>Configurar os métodos de autenticação

Referência ao seu planejamento da seção planejamento métodos de autenticação de palavra-passe deste documento.

1. Selecione o registo, em requerem utilizadores se registem ao iniciar sessão, selecione Sim e, em seguida, defina o número de dias antes da expiração e, em seguida, selecione guardar.
1. Selecione a notificação e configure por seu plano e, em seguida, selecionar guardar.
1. Selecione a personalização e configure por seu plano e, em seguida, selecionar guardar.
1. Selecione a integração no local e configure por seu plano e, em seguida, selecionar guardar.

### <a name="enable-sspr-in-windows"></a>Ativar SSPR no Windows

Dispositivos Windows 10 com a versão 1803 ou superior de qualquer associados ao Azure AD ou Azure AD híbrido pode repor as palavras-passe no ecrã de início de sessão do Windows. Informações e passos para configurar esse recurso podem ser encontrados no artigo [palavra-passe do Azure AD a partir do ecrã de início de sessão de reposição](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>Configurar a repetição de escrita de palavra-passe

Passos para configurar a repetição de escrita de palavra-passe para a sua organização pode ser encontrada no artigo [procedimentos: Configurar a repetição de escrita de palavra-passe](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>Gerir a SSPR

Funções necessárias para gerir os recursos associados a reposição de palavra-passe self-service.

| Função de negócio/pessoa | Função do Azure AD (se necessário) |
| :---: | :---: |
| Suporte técnico de nível 1 | Administrador de palavras-passe |
| Suporte técnico de nível 2 | Administrador de utilizadores |
| Administrador SSPR | Administrador global |

### <a name="support-scenarios"></a>Cenários de suporte

Para ativar o seu sucesso da equipe de suporte, pode criar uma FAQ com base em dúvidas que recebermos dos seus utilizadores. A tabela seguinte contém cenários comuns de suporte.

| Cenários | Descrição |
| --- | --- |
| O utilizador não tem quaisquer métodos de autenticação registados disponíveis | Um utilizador está a tentar repor a palavra-passe, mas não tem qualquer um dos métodos de autenticação que eles registado disponível (exemplo: eles deixou seu celular em casa e não é possível aceder ao e-mail) |
| Utilizador não é receber uma mensagem de texto ou chamada no respetivo office ou um número de telemóvel | Um utilizador está a tentar verificar sua identidade através do texto ou chamada, mas não está a receber uma chamada/texto. |
| Usuário não pode acessar o portal de reposição de palavra-passe | Um utilizador quer repor a palavra-passe, mas não está ativado para a reposição de palavra-passe e, portanto, não é possível aceder à página para atualizar as palavras-passe. |
| Utilizador não é possível definir uma nova palavra-passe | Um utilizador conclui a verificação durante o fluxo de reposição de palavra-passe, mas não é possível definir uma nova palavra-passe. |
| Utilizador não vê uma ligação de reposição de palavra-passe num dispositivo Windows 10 | Um utilizador está a tentar repor a palavra-passe de ecrã de bloqueio do Windows 10, mas o dispositivo não estiver seja associado ao Azure AD ou a política de dispositivo do Intune não está ativada |

Também poderá incluir informações como as seguintes opções para resolução de problemas adicionais.

- Os grupos que estão ativados para SSPR.
- Os métodos de autenticação estão configurados.
- As políticas de acesso relacionadas com no ou da rede empresarial.
- Passos para cenários comuns de resolução de problemas.

Também pode consultar a nossa documentação online sobre como resolver problemas de senhas de auto-atendimento repor para compreender os passos de resolução de problemas gerais para os cenários mais comuns do SSPR.

## <a name="next-steps"></a>Passos Seguintes

- [Considere a implementação de proteção de palavra-passe do Azure AD](concept-password-ban-bad.md)

- [Considere a implementação do Azure AD Smart Lockout](howto-password-smart-lockout.md)
