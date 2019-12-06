---
title: Implantação de redefinição de senha de autoatendimento-Azure Active Directory
description: Estratégia para a implementação bem-sucedida da redefinição de senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc2c68c53a7c03d1de08e5cde528f27aa61b0096
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847274"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Implementar a reposição personalizada de palavra-passe do Azure AD

> [!NOTE]
> Este guia explica a redefinição de senha de autoatendimento e como implantá-la. Se você estiver procurando a ferramenta de redefinição de senha de autoatendimento para voltar à sua conta, vá para [https://aka.ms/sspr](https://aka.ms/sspr). 

A SSPR (autoatendimento de redefinição de senha) é um recurso Azure Active Directory que permite aos funcionários redefinir suas senhas sem precisar entrar em contato com a equipe de ti. Os funcionários devem se registrar ou ser registrados para redefinição de senha de autoatendimento antes de usar o serviço. Durante o registro, o funcionário escolhe um ou mais métodos de autenticação habilitados pela organização.

O SSPR permite que os funcionários sejam desbloqueados rapidamente e continuem trabalhando independentemente de onde estiverem ou a hora do dia. Ao permitir que os usuários se desbloqueiem, sua organização pode reduzir o tempo não produtivo e os altos custos de suporte para os problemas mais comuns relacionados a senhas.

Ajude os usuários a se registrarem rapidamente implantando SSPR junto com outro aplicativo ou serviço em sua organização. Essa ação gerará um grande volume de entradas e irá orientar o registro.

Antes de implantar o SSPR, as organizações podem querer determinar a quantidade de redefinição de senha relacionada às chamadas de suporte técnico que ocorrem ao longo do tempo e o custo médio de cada chamada. Eles podem usar esses dados após a implantação para mostrar o valor que o SSPR está trazendo para sua organização.  

## <a name="how-sspr-works"></a>Como funciona a SSPR

1. Quando um usuário tenta redefinir uma senha, ele deve verificar seu método de autenticação registrado anteriormente ou métodos para provar sua identidade.
1. Em seguida, o usuário insere uma nova senha.
   1. Para usuários somente na nuvem, a nova senha é armazenada em Azure Active Directory. Para obter mais informações, consulte o artigo [como o SSPR funciona](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. Para usuários híbridos, a senha é gravada no Active Directory local por meio do serviço Azure AD Connect. Para obter mais informações, consulte o artigo [o que é Write-back de senha](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Considerações sobre licenciamento

Azure Active Directory é licenciado por usuário, o que significa que cada usuário precisa ter uma licença apropriada para os recursos que eles utilizam.

Mais informações sobre o licenciamento podem ser encontradas na [página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Habilitar o registro combinado para SSPR e MFA

A Microsoft recomenda que as organizações habilitem a experiência de registro combinada para SSPR e autenticação multifator. Quando você habilita essa experiência de registro combinada, os usuários só precisam selecionar suas informações de registro uma vez para habilitar os dois recursos.

![Registro de informações de segurança combinadas](./media/howto-sspr-deployment/combined-security-info.png)

A experiência de registro combinada não exige que as organizações habilitem o SSPR e a autenticação multifator do Azure para usar o. A experiência de registro combinada fornece às organizações uma experiência de usuário melhor em comparação com os componentes individuais tradicionais. Mais informações sobre o registro combinado e como habilitar o podem ser encontradas no artigo registro de [informações de segurança combinadas (versão prévia)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>Planejar a configuração

As configurações a seguir são necessárias para habilitar o SSPR juntamente com os valores recomendados.

| Área | Definição | Valor |
| --- | --- | --- |
| **Propriedades de SSPR** | Redefinição de senha de autoatendimento habilitada | Grupo **selecionado** para o piloto/ **tudo** para produção |
| **Métodos de autenticação** | Métodos de autenticação necessários para registrar | Sempre 1 mais do que o necessário para redefinir |
|   | Métodos de autenticação necessários para redefinir | Um ou dois |
| **Registo** | Exigir que os utilizadores se registem ao iniciar sessão | Sim |
|   | Número de dias até ser pedido aos utilizadores que reconfirmem as informações de autenticação | 90 – 180 dias |
| **Notificações** | Notificar os utilizadores sobre reposições de palavras-passe | Sim |
|   | Notificar todos os administradores quando outros administradores repõem as palavras-passe deles | Sim |
| **Customization** | Personalizar ligação do suporte técnico | Sim |
|   | URL ou e-mail do suporte técnico personalizado | Site de suporte ou endereço de email |
| **Integração no local** | Write-back de senhas para o AD local | Sim |
|   | Permitir que os usuários desbloqueiem a conta sem redefinir a senha | Sim |

### <a name="sspr-properties-recommendations"></a>Recomendações de propriedades de SSPR

Ao habilitar a redefinição de senha de autoatendimento, escolha um grupo de segurança a ser usado durante o piloto.

Quando você planeja iniciar o serviço mais amplamente, é recomendável usar a opção All para impor o SSPR para todos na organização. Se não for possível definir como todos, selecione o grupo de segurança do Azure AD apropriado ou o grupo do AD sincronizado com o Azure AD.

### <a name="authentication-methods"></a>Métodos de autenticação

Defina os métodos de autenticação necessários para se registrar em pelo menos um número maior que o necessário para a redefinição. Permitir vários dá aos usuários flexibilidade quando eles precisam ser redefinidos.

Defina o **número de métodos necessários para redefinir** para um nível apropriado para sua organização. Uma delas requer o menor conflito, enquanto duas podem aumentar sua postura de segurança.

Confira [o que são métodos de autenticação](concept-authentication-methods.md) para obter informações detalhadas sobre quais métodos de autenticação estão disponíveis para SSPR, perguntas de segurança predefinidas e como criar perguntas de segurança personalizadas.

### <a name="registration-settings"></a>Configurações de registro

Defina **exigir que os usuários se registrem ao entrar** em **Sim**. Essa configuração significa que os usuários são forçados a se registrar ao entrar, garantindo que todos os usuários estejam protegidos.

Defina **o número de dias antes que os usuários sejam solicitados a confirmar novamente suas informações de autenticação** entre **90** e **180** dias, a menos que sua organização tenha uma necessidade comercial por um período de tempo menor.

### <a name="notifications-settings"></a>Definições de notificações

Configure os **usuários de notificação em redefinições de senha** e **notifique todos os administradores quando outros administradores redefinirem sua senha** como **Sim**. A seleção de **Sim** em ambos aumenta a segurança, garantindo que os usuários saibam quando sua senha foi redefinida e que todos os administradores estão cientes quando um administrador altera uma senha. Se os usuários ou administradores receberem tal notificação e eles não tiverem iniciado a alteração, eles poderão relatar imediatamente uma possível violação de segurança.

### <a name="customization"></a>Personalização

É essencial personalizar o **email ou a URL da assistência técnica** para garantir que os usuários que tenham problemas possam obter ajuda rapidamente. Defina essa opção como um endereço de email comum da assistência técnica ou página da Web com a qual os usuários estão familiarizados.

### <a name="on-premises-integration"></a>Integração no local

Se você tiver um ambiente híbrido, verifique se **a gravação de senhas no AD local** está definida como **Sim**. Defina também a opção permitir que os usuários desbloqueiem a conta sem redefinir a senha como Sim, pois ela oferece mais flexibilidade.

### <a name="changingresetting-passwords-of-administrators"></a>Alterando/redefinindo senhas de administradores

As contas de administrador são contas especiais com permissões elevadas. Para protegê-los, as seguintes restrições se aplicam à alteração de senhas de administradores:

- Os administradores corporativos locais ou os administradores de domínio não podem redefinir sua senha por meio de SSPR. Eles só podem alterar sua senha em seu ambiente local. Portanto, recomendamos não sincronizar contas de administrador do AD local para o Azure AD.
- Um administrador não pode usar perguntas secretas & respostas como um método para redefinir a senha.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambientes com vários sistemas de gerenciamento de identidade

Se houver vários sistemas de gerenciamento de identidade em um ambiente, como os gerenciadores de identidade locais, como Oracle AM, SiteMinder ou outros sistemas, as senhas gravadas no Active Directory talvez precisem ser sincronizadas para os outros sistemas usando uma ferramenta como o serviço de notificação de alteração de senha (PCNS) com o MIM (Microsoft Identity Manager). Para encontrar informações sobre esse cenário mais complexo, consulte o artigo [implantar o serviço de notificação de alteração de senha do mim em um controlador de domínio](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>Planejar a implantação e o suporte para SSPR

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, eles normalmente fazem isso devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, certifique-se de que você esteja participando dos participantes certos e que as funções de Stakeholder no projeto sejam bem compreendidas ao documentar os participantes e suas informações de projeto e responsabilidade.

### <a name="communications-plan"></a>Plano de comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique-se proativamente com seus usuários como usar o serviço e o que eles podem fazer para obter ajuda se algo não funcionar conforme o esperado. Examine os [materiais de distribuição de redefinição de senha de autoatendimento no centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=56768) para obter ideias sobre como planejar sua estratégia de comunicação do usuário final.

### <a name="testing-plan"></a>Plano de teste

Para garantir que sua implantação funcione conforme o esperado, você deve planejar um conjunto de casos de teste que será usado para validar a implementação. A tabela a seguir inclui alguns cenários de teste úteis que você pode usar para documentar os resultados esperados de suas organizações com base em suas políticas.

| Caso prático | Resultado esperado |
| --- | --- |
| O portal do SSPR pode ser acessado de dentro da rede corporativa | Determinado pela sua organização |
| O portal do SSPR pode ser acessado de fora da rede corporativa | Determinado pela sua organização |
| Redefinir a senha do usuário do navegador quando o usuário não estiver habilitado para redefinição de senha | O usuário não consegue acessar o fluxo de redefinição de senha |
| Redefinir a senha do usuário do navegador quando o usuário não tiver se registrado para redefinição de senha | O usuário não consegue acessar o fluxo de redefinição de senha |
| O usuário entra quando o registro de redefinição de senha é imposto | O usuário é solicitado a registrar informações de segurança |
| O usuário entra quando o registro de redefinição de senha foi concluído | Não é solicitado que o usuário registre informações de segurança |
| O portal do SSPR é acessível quando o usuário não tem uma licença | Está acessível |
| Redefinir a senha de usuário da tela de bloqueio do dispositivo ingressado no Azure AD ou ingressado no Azure Active Directory após o registro do usuário | O usuário pode redefinir a senha |
| Os dados de registro e de uso do SSPR estão disponíveis para os administradores quase em tempo real | Está disponível por meio de logs de auditoria |

### <a name="support-plan"></a>Plano de suporte

Embora o SSPR normalmente não crie problemas de usuário, é importante que a equipe de suporte tenha preparado para lidar com problemas que possam surgir.

Embora um administrador possa alterar ou redefinir a senha para usuários finais por meio do portal do AD do Azure, é melhor ajudar a resolver o problema por meio de um processo de suporte de autoatendimento.

Na seção guia operacional deste documento, crie uma lista de casos de suporte e suas causas prováveis e crie um guia para resolução.

### <a name="auditing-and-reporting"></a>Auditoria e relatórios

Após a implantação, muitas organizações desejam saber como ou se a redefinição de senha de autoatendimento (SSPR) está realmente sendo usada. O recurso de relatório que o Azure Active Directory (Azure AD) fornece ajuda a responder perguntas usando relatórios predefinidos.

Os logs de auditoria para registro e redefinição de senha estão disponíveis por 30 dias. Portanto, se a auditoria de segurança dentro de uma empresa exigir maior retenção, os logs precisarão ser exportados e consumidos em uma ferramenta SIEM, como o [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk ou ArcSight.

Em uma tabela, como a abaixo, documente o agendamento de backup, o sistema e as partes responsáveis. Talvez você não precise de backups de auditoria e de relatórios separados, mas deve ter um backup separado do qual você pode se recuperar de um problema.

|   | Frequência de download | Sistema de destino | Parte responsável |
| --- | --- | --- | --- |
| Backup de auditoria |   |   |   |
| Backup de relatórios |   |   |   |
| Backup de recuperação de desastre |   |   |   |

## <a name="implementation"></a>Implementação

A implementação ocorre em três estágios:

- Configurar usuários e licenças
- Configurar o Azure AD SSPR para registro e autoatendimento
- Configurar Azure AD Connect para Write-back de senha

### <a name="communicate-the-change"></a>Comunicar a alteração

Comece a implementação do plano de comunicações que você desenvolveu na fase de planejamento.

### <a name="ensure-groups-are-created-and-populated"></a>Garantir que os grupos sejam criados e preenchidos

Consulte a seção planejando métodos de autenticação de senha e verifique se os grupos do piloto ou da implementação de produção estão disponíveis e se todos os usuários apropriados são adicionados aos grupos.

### <a name="apply-licenses"></a>Aplicar licenças

Os grupos que você vai implementar devem ter a licença do Azure AD Premium atribuída a eles. Você pode atribuir licenças diretamente ao grupo ou pode usar políticas de licença existentes, como por meio do PowerShell ou licenciamento baseado em grupo.

Informações sobre a atribuição de licenças a grupos de usuários podem ser encontradas no artigo [atribuir licenças a usuários por associação de grupo no Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Configurar a SSPR

#### <a name="enable-groups-for-sspr"></a>Habilitar grupos para SSPR

1. Acesse a portal do Azure com uma conta de administrador.
1. Selecione todos os serviços e, na caixa filtro, digite Azure Active Directory e, em seguida, selecione Azure Active Directory.
1. Na folha Active Directory, selecione redefinição de senha.
1. No painel Propriedades, selecione selecionado. Se você quiser que todos os usuários estejam habilitados, selecione tudo.
1. Na folha política de redefinição de senha padrão, digite o nome do primeiro grupo, selecione-o e, em seguida, clique em selecionar na parte inferior da tela e selecione salvar na parte superior da tela.
1. Repita esse processo para cada grupo.

#### <a name="configure-the-authentication-methods"></a>Configurar os métodos de autenticação

Faça referência ao seu planejamento na seção planejando métodos de autenticação de senha deste documento.

1. Selecione registro, em exigir que o usuário se registre ao entrar, selecione Sim e, em seguida, defina o número de dias antes da expiração e, em seguida, selecione salvar.
1. Selecione notificação e configure de acordo com seu plano e, em seguida, selecione salvar.
1. Selecione personalização e configurar de acordo com seu plano e, em seguida, selecione salvar.
1. Selecione integração local e configure de acordo com seu plano e, em seguida, selecione salvar.

### <a name="enable-sspr-in-windows"></a>Habilitar SSPR no Windows

Dispositivos Windows 10 que executam a versão 1803 ou superior que são ingressadas no Azure AD ou no Azure AD híbrido podem redefinir suas senhas na tela de logon do Windows. As informações e as etapas para configurar esse recurso podem ser encontradas no artigo [redefinição de senha do Azure ad na tela de logon](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>Configurar write-back de senha

As etapas para configurar o Write-back de senha para sua organização podem ser encontradas no artigo [como: configurar o Write-back de senha](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>Gerenciar SSPR

Funções necessárias para gerenciar recursos associados à redefinição de senha de autoatendimento.

| Função corporativa/persona | Função do Azure AD (se necessário) |
| :---: | :---: |
| Suporte técnico nível 1 | Administrador de palavras-passe |
| Suporte técnico nível 2 | Administrador do usuário |
| Administrador do SSPR | Administrador global |

### <a name="support-scenarios"></a>Cenários de suporte

Para habilitar o sucesso da equipe de suporte, você pode criar perguntas frequentes com base nas perguntas que você recebe dos usuários. A tabela a seguir contém cenários de suporte comuns.

| Cenários | Descrição |
| --- | --- |
| O usuário não tem nenhum método de autenticação registrado disponível | Um usuário está tentando redefinir sua senha, mas não tem nenhum dos métodos de autenticação que eles registraram disponíveis (exemplo: elas deixaram seu telefone celular em casa e não podem acessar o email) |
| O usuário não está recebendo um texto ou uma chamada em seu escritório ou telefone celular | Um usuário está tentando verificar sua identidade por meio de texto ou chamada, mas não está recebendo um texto/chamada. |
| O usuário não pode acessar o portal de redefinição de senha | Um usuário deseja redefinir sua senha, mas não está habilitado para redefinição de senha e, portanto, não pode acessar a página para atualizar senhas. |
| O usuário não pode definir uma nova senha | Um usuário conclui a verificação durante o fluxo de redefinição de senha, mas não pode definir uma nova senha. |
| O usuário não vê um link Redefinir senha em um dispositivo Windows 10 | Um usuário está tentando redefinir a senha da tela de bloqueio do Windows 10, mas o dispositivo não está associado ao Azure AD ou a política de dispositivo do Intune não está habilitada |

Talvez você também queira incluir informações como as seguintes para solução de problemas adicionais.

- Quais grupos estão habilitados para SSPR.
- Quais métodos de autenticação são configurados.
- As políticas de acesso relacionadas ao na rede corporativa ou da.
- Etapas de solução de problemas para cenários comuns.

Você também pode consultar nossa documentação online sobre solução de problemas de redefinição de senha de autoatendimento para entender as etapas gerais de solução de problemas para os cenários de SSPR mais comuns.

## <a name="next-steps"></a>Passos seguintes

- [Considere implementar a proteção de senha do Azure AD](concept-password-ban-bad.md)

- [Considere implementar o bloqueio inteligente do Azure AD](howto-password-smart-lockout.md)
