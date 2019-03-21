---
title: Recursos de segurança para ajudar a proteger cópias de segurança híbridas que utilizam o Azure Backup
description: Saiba como utilizar funcionalidades de segurança no Azure Backup para fazer cópias de segurança mais seguro
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2017
ms.author: trinadhk
ms.openlocfilehash: 2ba94963238cd5ee96df5c178a072addc5ddd75e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082930"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Recursos de segurança para ajudar a proteger cópias de segurança híbridas que utilizam o Azure Backup
Preocupações sobre problemas de segurança, como software maligno, ransomware e intrusões, estão a aumentar. Esses problemas de segurança podem ser caras, em termos de dinheiro e dados. Para proteger contra esses ataques, a cópia de segurança do Azure agora fornece as funcionalidades de segurança para ajudar a proteger cópias de segurança híbridas. Este artigo aborda como ativar e utilizar estas funcionalidades, utilizando um agente de serviços de recuperação do Azure e o servidor de cópia de segurança do Azure. Estas funcionalidades incluem:

- **Prevenção**. Uma camada adicional de autenticação é adicionada sempre que uma operação crítica, como alterar uma frase de acesso é executada. É esta validação para garantir que essas operações podem ser executadas apenas por utilizadores com credenciais do Azure válidas.
- **Alertas**. Uma notificação por e-mail é enviada para o administrador de subscrição, sempre que uma operação crítica, como a eliminação de dados de cópia de segurança é executada. Este e-mail garante que o utilizador é notificado de rapidamente sobre tais ações.
- **Recuperação**. Dados de cópia de segurança eliminados são retidos adicional para 14 dias a contar da data da eliminação. Isto garante a capacidade de recuperação dos dados dentro de um determinado período de tempo, portanto, não há nenhuma perda de dados, mesmo que um ataque acontece. Além disso, um maior número de pontos de recuperação mínimo é mantido para proteger contra dados danificados.

> [!NOTE]
> Recursos de segurança não devem ser ativados se estiver usando a infraestrutura de cópia de segurança de VM de serviço (IaaS). Estas funcionalidades ainda não estão disponíveis para cópia de segurança de VM de IaaS, portanto, o que permite que eles não terão qualquer impacto. Recursos de segurança devem ser ativados apenas se estiver a utilizar: <br/>
>  * **O agente de cópia de segurança do Azure**. Versão de agente mínima 2.0.9052. Após ativar estas funcionalidades, deve atualizar para esta versão do agente para executar operações críticas. <br/>
>  * **O Azure Backup Server**. Versão mínima do Microsoft Azure Backup agent 2.0.9052 com o Azure Backup Server atualização 1. <br/>
>  * **System Center Data Protection Manager**. Azure Backup agent a versão mínima 2.0.9052 com o Data Protection Manager 2012 R2 UR12 ou o Data Protection Manager 2016 UR2. <br/>


> [!NOTE]
> Esses recursos estão disponíveis apenas para o Cofre dos serviços de recuperação. Todos os cofres de serviços de recuperação criados recentemente têm estas funcionalidades ativadas por predefinição. Para os cofres de serviços de recuperação existentes, os utilizadores ativar estas funcionalidades, utilizando os passos mencionados na secção seguinte. Depois das funcionalidades estão ativadas, estas são aplicadas a todos os serviços de recuperação computadores do agente, instâncias de servidor de cópia de segurança do Azure, e os servidores de Data Protection Manager registados com o cofre. A ativação desta definição é uma ação única e não é possível desativar estas funcionalidades após ativá-las.
>

## <a name="enable-security-features"></a>Ativar funcionalidades de segurança
Se estiver a criar um cofre dos serviços de recuperação, pode utilizar todas as funcionalidades de segurança. Se estiver a trabalhar com um cofre existente, ative funcionalidades de segurança ao seguir estes passos:

1. Inicie sessão no portal do Azure, utilizando as credenciais do Azure.
2. Selecione **navegue**e escreva **dos serviços de recuperação**.

    ![Opção de procurar portal de captura de ecrã do Azure](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    É apresentada a lista dos cofres dos serviços de recuperação. Na lista, selecione um cofre. O dashboard do cofre selecionado é aberto.
3. Na lista de itens que é apresentado no cofre, em **configurações**, clique em **propriedades**.

    ![Opções de Cofre de serviços de captura de ecrã de recuperação](./media/backup-azure-security-feature/vault-list-properties.png)
4. Sob **definições de segurança**, clique em **atualização**.

    ![Propriedades do Cofre de serviços de captura de ecrã de recuperação](./media/backup-azure-security-feature/security-settings-update.png)

    Link de atualização abre o **definições de segurança** painel, que fornece um resumo dos recursos e permite-lhe ativá-los.
5. Na lista pendente **ter que configurou o multi-factor Authentication?**, selecione um valor para confirmar se ativou [multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md). Se estiver ativada, é-lhe perguntado para autenticar a partir de outro dispositivo (por exemplo, um telemóvel) ao iniciar sessão no portal do Azure.

   Quando efetua operações críticas na cópia de segurança, terá de introduzir uma PIN, disponível no portal do Azure de segurança. Ativar a multi-factor Authentication adiciona uma camada de segurança. Apenas os utilizadores com credenciais do Azure válidas autorizados e autenticados a partir de um segundo dispositivo, pode aceder ao portal do Azure.
6. Para guardar as definições de segurança, selecione **habilitar** e clique em **guardar**. Pode selecionar **ativar** apenas depois de selecionar um valor a partir do **ter que configurou o multi-factor Authentication?** lista no passo anterior.

    ![Captura de ecrã de definições de segurança](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Dados de cópia de segurança de recuperação eliminado
Cópia de segurança retém os dados de cópia de segurança eliminados durante um adicional de 14 dias e não o elimina imediatamente se o **parar cópia de segurança com eliminação de dados de cópia de segurança** operação é executada. Para restaurar esses dados no período de 14 dias, siga os passos seguintes, consoante o que estiver a utilizar:

Para **agente dos serviços de recuperação do Azure** utilizadores:

1. Se o computador em que ocorreram cópias de segurança ainda esteja disponível, voltar a proteger as origens de dados eliminados e utilizar o [recuperar os dados na mesma máquina](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) nos serviços de recuperação do Azure, para recuperar todos os pontos de recuperação antigos.
2. Se este computador não estiver disponível, utilize [recuperar para uma máquina alternativa](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) para utilizar outro computador de serviços de recuperação do Azure para obter esses dados.

Para **Azure Backup Server** utilizadores:

1. Se o servidor em que ocorreram cópias de segurança ainda esteja disponível, voltar a proteger as origens de dados eliminados e utilizar o **recuperar dados** funcionalidade para recuperar todos os pontos de recuperação antigos.
2. Se este servidor não estiver disponível, utilize [recuperar dados a partir de outro servidor de cópia de segurança do Azure](backup-azure-alternate-dpm-server.md) utilizar outra instância de servidor de cópia de segurança do Azure para obter esses dados.

Para **Data Protection Manager** utilizadores:

1. Se o servidor em que ocorreram cópias de segurança ainda esteja disponível, voltar a proteger as origens de dados eliminados e utilizar o **recuperar dados** funcionalidade para recuperar todos os pontos de recuperação antigos.
2. Se este servidor não estiver disponível, utilize [adicionar DPM externo](backup-azure-alternate-dpm-server.md) para utilizar outro servidor do Data Protection Manager para obter esses dados.

## <a name="prevent-attacks"></a>Impedir ataques
Verificações foram adicionadas para se certificar de que apenas os utilizadores válidos podem desempenhar várias operações. Estes incluem-se ao adicionar uma camada adicional de autenticação e a manutenção de um período de retenção mínimo para fins de recuperação.

### <a name="authentication-to-perform-critical-operations"></a>Autenticação para realizar operações críticas
Como parte da adição de uma camada adicional de autenticação para operações críticas, lhe for pedido para introduzir um PIN de segurança quando efetuar **parar proteção com eliminação de dados** e **frase de acesso de alteração** operações.

> [!NOTE]
> 
> Atualmente, o pin de segurança não é suportada para **parar proteção com eliminação de dados** do DPM e o MABS.

Para receber este PIN:

1. Inicie sessão no Portal do Azure.
2. Navegue até **cofre dos serviços de recuperação** > **definições** > **propriedades**.
3. Sob **PIN de segurança**, clique em **gerar**. Esta ação abre um painel que contém o PIN de ser introduzido na interface de usuário do agente dos serviços de recuperação do Azure.
    Este PIN é válido por apenas cinco minutos e, é gerado automaticamente depois desse período.

### <a name="maintain-a-minimum-retention-range"></a>Manter um período de retenção mínimo
Para garantir que há sempre um número válido de pontos de recuperação disponíveis, foram adicionadas as seguintes verificações:

- Para uma retenção diária, um mínimo de **sete** dias de retenção devem ser feitos.
- Para uma retenção semanal, um mínimo de **quatro** semanas de retenção devem ser feitas.
- Para uma retenção mensal, um mínimo de **três** meses de retenção devem ser feitas.
- Para uma retenção anual, um mínimo de **um** ano de retenção deve ser feito.

## <a name="notifications-for-critical-operations"></a>Notificações para operações críticas
Normalmente, quando é executada uma operação crítica, o administrador da subscrição é enviado uma notificação por e-mail com detalhes sobre a operação. Pode configurar os destinatários de e-mail adicionais para estas notificações com o portal do Azure.

Os recursos de segurança mencionados neste artigo fornecem mecanismos de defesa contra ataques direcionados. Mais importante, se ocorrer um ataque, estas funcionalidades dão-lhe a capacidade de recuperar os seus dados.

## <a name="troubleshooting-errors"></a>Resolução de erros
| Operação | Detalhes do erro | Resolução |
| --- | --- | --- |
| Alteração de política |Não foi possível modificar a política de cópia de segurança. Erro: A operação atual falhou devido a um erro de serviço interno [0x29834]. Repita a operação após algum tempo. Se o problema persistir, contacte o suporte da Microsoft. |**Causa:**<br/>Este erro é quando as definições de segurança estiverem ativadas, tente reduzir o período de retenção abaixo os valores mínimo especificado acima e estiver a utilizar a versão não suportada (as versões suportadas são especificadas na primeira nota deste artigo). <br/>**Ação recomendada:**<br/> Neste caso, deve definir o período de retenção acima de retenção mínimo período especificado (sete dias para diariamente, quatro semanas para semanais, três semanas para mensal ou um ano para anual) continuar com a política relacionadas com atualizações. Opcionalmente, a abordagem preferencial seria para atualizar o agente de cópia de segurança, servidor de cópia de segurança do Azure e/ou DPM UR para tirar partido de todas as atualizações de segurança. |
| Alterar frase de acesso |PIN de segurança introduzido está incorreto. (ID: Forneça 100130) o PIN de segurança correto para concluir esta operação. |**Causa:**<br/> Este erro é fornecido ao introduzir o PIN de segurança inválido ou expirou ao efetuar a operação crítica (como alterar frase de acesso). <br/>**Ação recomendada:**<br/> Para concluir a operação, tem de introduzir o PIN de segurança válido. Para obter o PIN, inicie sessão no portal do Azure e navegue para cofre dos serviços de recuperação > Definições > propriedades > gerar PIN de segurança. Utilize este PIN para alterar a frase de acesso. |
| Alterar frase de acesso |Falha na operação. ID: 120002 |**Causa:**<br/>Este erro é quando as definições de segurança estiverem ativadas, tentar alterar frase de acesso e estiver a utilizar a versão não suportada (versões válidas especificadas na primeira nota deste artigo).<br/>**Ação recomendada:**<br/> Para alterar a frase de acesso, primeiro tem de atualizar o agente de cópia de segurança para a versão mínima 2.0.9052 mínimo, o servidor de cópia de segurança do Azure para atualização mínima 1, e/ou DPM mínima do DPM 2012 R2 UR12 ou DPM 2016 UR2 (download links a seguir), em seguida, introduza o PIN de segurança válido. Para obter o PIN, inicie sessão no portal do Azure e navegue para cofre dos serviços de recuperação > Definições > propriedades > gerar PIN de segurança. Utilize este PIN para alterar a frase de acesso. |

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Cofre de serviços de recuperação do Azure](backup-azure-vms-first-look-arm.md) para ativar estas funcionalidades.
* [Transferir o agente dos serviços de recuperação do Azure mais recente](https://aka.ms/azurebackup_agent) para ajudar a proteger os computadores Windows e proteger os seus dados de cópia de segurança contra ataques.
* [Transferir o servidor de cópia de segurança do Azure mais recente](https://aka.ms/latest_azurebackupserver) para ajudar a proteger cargas de trabalho e proteger os seus dados de cópia de segurança contra ataques.
* [Transferir UR12 para System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) ou [transferir UR2 para System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) para ajudar a proteger cargas de trabalho e proteger os seus dados de cópia de segurança contra ataques.
