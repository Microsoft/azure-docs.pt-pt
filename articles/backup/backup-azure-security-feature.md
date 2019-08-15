---
title: Recursos de segurança para ajudar a proteger os backups híbridos que usam o backup do Azure
description: Saiba como usar os recursos de segurança no backup do Azure para tornar os backups mais seguros
ms.reviewer: utraghuv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2017
ms.author: dacurwin
ms.openlocfilehash: 2cd298323d8f455010978361078d474415e77dfa
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954531"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Recursos de segurança para ajudar a proteger os backups híbridos que usam o backup do Azure
As preocupações sobre problemas de segurança, como malware, ransomware e intrusão, estão aumentando. Esses problemas de segurança podem ser dispendiosos, em termos de dinheiro e dados. Para se proteger contra tais ataques, o backup do Azure agora fornece recursos de segurança para ajudar a proteger backups híbridos. Este artigo aborda como habilitar e usar esses recursos usando um agente dos serviços de recuperação do Azure e Servidor de Backup do Azure. As funcionalidades incluem:

- **Prevenção**. Uma camada adicional de autenticação é adicionada sempre que uma operação crítica, como alterar uma frase secreta, é executada. Essa validação é para garantir que essas operações possam ser executadas somente por usuários que têm credenciais válidas do Azure.
- **Alertas**. Uma notificação por email é enviada para o administrador da assinatura sempre que uma operação crítica, como excluir dados de backup, é executada. Esse email garante que o usuário seja notificado rapidamente sobre tais ações.
- **Recuperação**. Os dados de backup excluídos são retidos por mais 14 dias a partir da data da exclusão. Isso garante a capacidade de recuperação dos dados em um determinado período de tempo, portanto, não haverá perda de dados, mesmo se ocorrer um ataque. Além disso, um número maior de pontos de recuperação mínimos é mantido para proteção contra dados corrompidos.

> [!NOTE]
> Os recursos de segurança não devem ser habilitados se você estiver usando o backup de VM de IaaS (infraestrutura como serviço). Esses recursos ainda não estão disponíveis para backup de VM IaaS, portanto, habilitá-los não terá nenhum impacto. Os recursos de segurança só devem ser habilitados se você estiver usando: <br/>
>  * **Agente de backup do Azure**. Versão mínima do agente 2.0.9052. Depois de habilitar esses recursos, você deve atualizar para essa versão do agente para executar operações críticas. <br/>
>  * **Servidor de backup do Azure**. Versão mínima do agente de backup do Azure 2.0.9052 com Servidor de Backup do Azure atualização 1. <br/>
>  * **Data Protection Manager do System Center**. Versão mínima do agente de backup do Azure 2.0.9052 com Data Protection Manager 2012 R2 UR12 ou Data Protection Manager 2016 UR2. <br/>


> [!NOTE]
> Esses recursos estão disponíveis apenas para o cofre dos serviços de recuperação. Todos os cofres dos serviços de recuperação recém-criados têm esses recursos habilitados por padrão. Para os cofres dos serviços de recuperação existentes, os usuários habilitam esses recursos usando as etapas mencionadas na seção a seguir. Depois que os recursos são habilitados, eles se aplicam a todos os computadores do agente de serviços de recuperação, Servidor de Backup do Azure instâncias e servidores de Data Protection Manager registrados com o cofre. A habilitação dessa configuração é uma ação única, e você não pode desabilitar esses recursos depois de habilitá-los.
>

## <a name="enable-security-features"></a>Habilitar recursos de segurança
Se você estiver criando um cofre dos serviços de recuperação, poderá usar todos os recursos de segurança. Se você estiver trabalhando com um cofre existente, habilite os recursos de segurança seguindo estas etapas:

1. Entre no portal do Azure usando suas credenciais do Azure.
2. Selecione **procurar**e digite **serviços de recuperação**.

    ![Captura de tela da opção portal do Azure procurar](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    É apresentada a lista dos cofres dos serviços de recuperação. Nessa lista, selecione um cofre. O dashboard do cofre selecionado é aberto.
3. Na lista de itens que aparece no cofre, em **configurações**, clique em **Propriedades**.

    ![Captura de tela das opções do cofre de serviços de recuperação](./media/backup-azure-security-feature/vault-list-properties.png)
4. Em **configurações de segurança**, clique em **Atualizar**.

    ![Captura de tela das propriedades do cofre dos serviços de recuperação](./media/backup-azure-security-feature/security-settings-update.png)

    O link de atualização abre a folha **configurações de segurança** , que fornece um resumo dos recursos e permite que você os habilite.
5. Na lista suspensa **você configurou a autenticação multifator do Azure?** , selecione um valor para confirmar se você habilitou a [autenticação multifator do Azure](../active-directory/authentication/multi-factor-authentication.md). Se estiver habilitado, você será solicitado a autenticar de outro dispositivo (por exemplo, um telefone celular) ao entrar no portal do Azure.

   Ao executar operações críticas no backup, você precisa inserir um PIN de segurança, disponível no portal do Azure. A habilitação da autenticação multifator do Azure adiciona uma camada de segurança. Somente usuários autorizados com credenciais válidas do Azure e autenticados de um segundo dispositivo podem acessar o portal do Azure.
6. Para salvar as configurações de segurança, selecione **habilitar** e clique em **salvar**. Você pode selecionar **habilitar** somente depois de selecionar um valor da lista **você configurou a autenticação multifator do Azure?** na etapa anterior.

    ![Captura de tela das configurações de segurança](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Recuperar dados de backup excluídos
O backup mantém os dados de backup excluídos por mais 14 dias e não os exclui imediatamente se a operação **parar backup com excluir dados de backup** for executada. Para restaurar esses dados no período de 14 dias, execute as seguintes etapas, dependendo do que você está usando:

Para usuários do **agente dos serviços de recuperação do Azure** :

1. Se o computador onde os backups estavam ocorrendo ainda estiver disponível, proteja novamente as fontes de dados excluídas e use os dados de recuperação [para o mesmo computador](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) nos serviços de recuperação do Azure, para recuperar-se de todos os pontos de recuperação antigos.
2. Se este computador não estiver disponível, use [recuperar em um computador alternativo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) para usar outro computador dos serviços de recuperação do Azure para obter esses dados.

Para **servidor de backup do Azure** usuários:

1. Se o servidor onde os backups estavam ocorrendo ainda estiver disponível, proteja novamente as fontes de dados excluídas e use o recurso **recuperar dados** para se recuperar de todos os pontos de recuperação antigos.
2. Se esse servidor não estiver disponível, use [recuperar dados de outro servidor de backup do Azure](backup-azure-alternate-dpm-server.md) para usar outra instância de servidor de backup do Azure para obter esses dados.

Para **Data Protection Manager** usuários:

1. Se o servidor onde os backups estavam ocorrendo ainda estiver disponível, proteja novamente as fontes de dados excluídas e use o recurso **recuperar dados** para se recuperar de todos os pontos de recuperação antigos.
2. Se esse servidor não estiver disponível, use [Adicionar DPM externo](backup-azure-alternate-dpm-server.md) para usar outro servidor de Data Protection Manager para obter esses dados.

## <a name="prevent-attacks"></a>Impedir ataques
As verificações foram adicionadas para garantir que apenas usuários válidos possam executar várias operações. Isso inclui adicionar uma camada extra de autenticação e manter um intervalo de retenção mínimo para fins de recuperação.

### <a name="authentication-to-perform-critical-operations"></a>Autenticação para executar operações críticas
Como parte da adição de uma camada extra de autenticação para operações críticas, é solicitado que você insira um PIN de segurança ao executar **parar proteção com excluir dados** e **alterar** as operações de senha.

> [!NOTE]
> 
> Atualmente, o PIN de segurança não tem suporte para **interromper a proteção com excluir dados** para o DPM e o mAbs.

Para receber este PIN:

1. Inicie sessão no Portal do Azure.
2. Navegue até **serviços de recuperação** > **configurações** > do cofre**Propriedades**.
3. Em **PIN de segurança**, clique em **gerar**. Isso abre uma folha que contém o PIN a ser inserido na interface do usuário do agente dos serviços de recuperação do Azure.
    Esse PIN é válido por apenas cinco minutos e é gerado automaticamente após esse período.

### <a name="maintain-a-minimum-retention-range"></a>Manter um período de retenção mínimo
Para garantir que sempre haja um número válido de pontos de recuperação disponíveis, as seguintes verificações foram adicionadas:

- Para a retenção diária, um mínimo de **sete** dias de retenção deve ser feito.
- Para a retenção semanal, um mínimo de **quatro** semanas de retenção deve ser feito.
- Para retenção mensal, um mínimo de **três** meses de retenção deve ser feito.
- Para a retenção anual, um mínimo de **um** ano de retenção deve ser feito.

## <a name="notifications-for-critical-operations"></a>Notificações para operações críticas
Normalmente, quando uma operação crítica é executada, o administrador da assinatura recebe uma notificação por email com detalhes sobre a operação. Você pode configurar destinatários de email adicionais para essas notificações usando o portal do Azure.

Os recursos de segurança mencionados neste artigo fornecem mecanismos de defesa contra ataques direcionados. O mais importante é que, se ocorrer um ataque, esses recursos lhe darão a capacidade de recuperar seus dados.

## <a name="troubleshooting-errors"></a>Resolução de erros
| Operação | Detalhes do erro | Resolução |
| --- | --- | --- |
| Alteração de política |Não foi possível modificar a política de backup. Erro: A operação atual falhou devido a um erro de serviço interno [0x29834]. Repita a operação após algum tempo. Se o problema persistir, contacte o suporte da Microsoft. |**Faz**<br/>Este erro ocorre quando as configurações de segurança estão habilitadas, você tenta reduzir o período de retenção abaixo dos valores mínimos especificados acima e você está em uma versão sem suporte (as versões com suporte são especificadas na primeira observação deste artigo). <br/>**Ação recomendada:**<br/> Nesse caso, você deve definir o período de retenção acima do período de retenção mínimo especificado (sete dias para diariamente, quatro semanas para semana, três semanas para mensal ou um ano para o anual) para prosseguir com as atualizações relacionadas à política. Opcionalmente, a abordagem preferida seria atualizar o agente de backup, Servidor de Backup do Azure e/ou o UR do DPM para aproveitar todas as atualizações de segurança. |
| Alterar frase secreta |O PIN de segurança inserido está incorreto. (ID: 100130) forneça o PIN de segurança correto para concluir esta operação. |**Faz**<br/> Esse erro ocorre quando você insere um PIN de segurança inválido ou expirado ao executar uma operação crítica (como alterar frase secreta). <br/>**Ação recomendada:**<br/> Para concluir a operação, você deve inserir um PIN de segurança válido. Para obter o PIN, faça logon no portal do Azure e navegue até o cofre dos serviços de recuperação > Configurações > Propriedades > gerar PIN de segurança. Use este PIN para alterar a frase secreta. |
| Alterar frase secreta |Falha na operação. ID: 120002 |**Faz**<br/>Esse erro ocorre quando as configurações de segurança estão habilitadas, você tenta alterar a senha e você está em uma versão sem suporte (versões válidas especificadas na primeira observação deste artigo).<br/>**Ação recomendada:**<br/> Para alterar a senha, primeiro você deve atualizar o agente de backup para o mínimo de 2.0.9052 de versão, o servidor de backup do Azure para a atualização mínima 1 e/ou o DPM para o DPM 2012 R2 UR12 ou o DPM 2016 UR2 (baixar links abaixo) e, em seguida, inserir um PIN de segurança válido. Para obter o PIN, faça logon no portal do Azure e navegue até o cofre dos serviços de recuperação > Configurações > Propriedades > gerar PIN de segurança. Use este PIN para alterar a frase secreta. |

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao cofre dos serviços de recuperação do Azure](backup-azure-vms-first-look-arm.md) para habilitar esses recursos.
* [Baixe o agente dos serviços de recuperação do Azure mais recente](https://aka.ms/azurebackup_agent) para ajudar a proteger computadores Windows e proteger seus dados de backup contra ataques.
* [Baixe as servidor de backup do Azure mais recentes](https://aka.ms/latest_azurebackupserver) para ajudar a proteger as cargas de trabalho e proteger seus dados de backup contra ataques.
* [Baixe o UR12 para System center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) ou [Baixe UR2 para system Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) para ajudar a proteger as cargas de trabalho e proteger seus dados de backup contra ataques.
