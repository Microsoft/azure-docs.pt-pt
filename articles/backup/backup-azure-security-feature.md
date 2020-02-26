---
title: Funcionalidades de segurança que protegem backups híbridos
description: Saiba como usar funcionalidades de segurança no Azure Backup para tornar as cópias de segurança mais seguras
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 7213f26493a118c2cb32f8f9935b4954176b99a2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586398"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funcionalidades de segurança para ajudar a proteger backups híbridos que usam Backup Azure

As preocupações com questões de segurança, como malware, ransomware e intrusão, estão a aumentar. Estas questões de segurança podem ser dispendiosas, tanto em termos de dinheiro como de dados. Para se proteger de tais ataques, o Azure Backup fornece agora funcionalidades de segurança para ajudar a proteger os backups híbridos. Este artigo abrange como ativar e utilizar estas funcionalidades, utilizando um agente dos Serviços de Recuperação Azure e o Servidor de Backup Azure. As funcionalidades incluem:

- **Prevenção.** Uma camada adicional de autenticação é adicionada sempre que uma operação crítica como mudar uma palavra-passe é executada. Esta validação destina-se a garantir que tais operações só podem ser realizadas por utilizadores que tenham credenciais Azure válidas.
- **Alertando.** Uma notificação por e-mail é enviada para o administrador de subscrição sempre que uma operação crítica como a abater dados de backup é realizada. Este e-mail garante que o utilizador é notificado rapidamente sobre tais ações.
- **Recuperação.** Os dados de cópia de segurança eliminados são conservados por mais 14 dias a contar da data da eliminação. Isto garante a recuperabilidade dos dados num determinado período de tempo, pelo que não existe perda de dados mesmo que um ataque ocorra. Além disso, um maior número de pontos mínimos de recuperação são mantidos para proteger contra dados corruptos.

> [!NOTE]
> As funcionalidades de segurança não devem ser ativadas se estiver a utilizar a infraestrutura como cópia de segurança VM de serviço (IaaS). Estas funcionalidades ainda não estão disponíveis para backup IaaS VM, pelo que permitir-lhes não terá qualquer impacto. As funcionalidades de segurança só devem ser ativadas se estiver a utilizar: <br/>
>  * **Agente de backup Azure.** Versão de agente mínimo 2.0.9052. Depois de ter ativado estas funcionalidades, deverá fazer o upgrade para esta versão do agente para realizar operações críticas. <br/>
>  * **Servidor de backup Azure**. Versão 2.0.9052 do agente de backup Minimum Azure com atualização do Servidor de Backup Azure 1. <br/>
>  * **Gestor de Proteção de Dados do Centro**de Sistemas . Versão 2.0.9052 do agente de backup Minimum Azure 2.0.9052 com Data Protection Manager 2012 R2 UR12 ou Data Protection Manager 2016 UR2. <br/>


> [!NOTE]
> Estas funcionalidades estão disponíveis apenas para o cofre dos Serviços de Recuperação. Todos os cofres recém-criados dos Serviços de Recuperação têm estas funcionalidades ativadas por padrão. Para os cofres dos Serviços de Recuperação existentes, os utilizadores permitem estas funcionalidades utilizando os passos mencionados na secção seguinte. Após a ativação das funcionalidades, aplicam-se a todos os computadores do agente De recuperação, às instâncias do Servidor de Backup Azure e aos servidores do Gestor de Proteção de Dados registados no cofre. Permitir esta definição é uma ação única e não pode desativar estas funcionalidades depois de as ativar.
>

## <a name="enable-security-features"></a>Ativar funcionalidades de segurança

Se estiver a criar um cofre dos Serviços de Recuperação, pode utilizar todos os recursos de segurança. Se estiver a trabalhar com um cofre existente, ative as funcionalidades de segurança seguindo estes passos:

1. Inscreva-se no portal Azure utilizando as suas credenciais Azure.
2. Selecione **Browse,** type **Recovery Services**.

    ![Screenshot do portal Azure Opção Browse](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    É apresentada a lista dos cofres dos serviços de recuperação. Desta lista, selecione um cofre. O dashboard do cofre selecionado é aberto.
3. A partir da lista de itens que aparecem sob o cofre, em **Definições,** clique em **Propriedades**.

    ![Screenshot das opções de cofre de serviços de recuperação](./media/backup-azure-security-feature/vault-list-properties.png)
4. Em Definições de **Segurança,** clique em **Atualizar**.

    ![Screenshot das propriedades do cofre dos Serviços de Recuperação](./media/backup-azure-security-feature/security-settings-update.png)

    O link de atualização abre a lâmina definições de **segurança,** que fornece um resumo das funcionalidades e permite-lhe ativar.
5. A partir da lista de **lançamentos Configurau a Autenticação multi-factor Do Azure?** [](../active-directory/authentication/multi-factor-authentication.md) Se estiver ativado, é-lhe pedido que autenticasse a partir de outro dispositivo (por exemplo, um telemóvel) enquanto se insere no portal Azure.

   Quando realiza operações críticas em Backup, tem de introduzir um PIN de segurança, disponível no portal Azure. Ativar a autenticação multi-factor Azure adiciona uma camada de segurança. Apenas utilizadores autorizados com credenciais Azure válidas, e autenticados a partir de um segundo dispositivo, podem aceder ao portal Azure.
6. Para guardar as definições de segurança, selecione **'Activar'** e clicar em **Guardar**. Só pode selecionar **Ativar** depois de selecionar um valor da lista de **autenticação multifactor Azure configurado?**

    ![Screenshot das definições de segurança](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Recuperar dados de backup eliminados

A cópia de segurança retém os dados de backup eliminados durante mais 14 dias e não os apaga imediatamente se for realizada a cópia de segurança Stop com a eliminação da operação de dados de **backup.** Para restaurar estes dados no período de 14 dias, tome as seguintes etapas, dependendo do que estiver a utilizar:

Para utilizadores de agentes de serviços de **recuperação azure:**

1. Se o computador onde as cópias de segurança estavam a acontecer ainda estiver disponível, reproteja as fontes de dados eliminadas e utilize os [dados da Recuperação para a mesma máquina](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) nos Serviços de Recuperação do Azure, para recuperar de todos os antigos pontos de recuperação.
2. Se este computador não estiver disponível, utilize a [Recuperar para uma máquina alternativa](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) para utilizar outro computador dos Serviços de Recuperação Do Azure para obter estes dados.

Para utilizadores **do Servidor de Backup Azure:**

1. Se o servidor onde as cópias de segurança estavam a acontecer ainda estiver disponível, reproteja as fontes de dados eliminadas e utilize a funcionalidade **Recuperar Dados** para recuperar de todos os pontos de recuperação antigos.
2. Se este servidor não estiver disponível, utilize [os dados de Recuperação de outro Servidor](backup-azure-alternate-dpm-server.md) de Backup Azure para utilizar outra instância do Servidor de Backup Azure para obter estes dados.

Para utilizadores **do Gestor de Proteção de Dados:**

1. Se o servidor onde as cópias de segurança estavam a acontecer ainda estiver disponível, reproteja as fontes de dados eliminadas e utilize a funcionalidade **Recuperar Dados** para recuperar de todos os pontos de recuperação antigos.
2. Se este servidor não estiver disponível, utilize [o Add External DPM](backup-azure-alternate-dpm-server.md) para utilizar outro servidor do Gestor de Proteção de Dados para obter estes dados.

## <a name="prevent-attacks"></a>Prevenir ataques

Foram adicionadas verificações para garantir que apenas utilizadores válidos podem realizar várias operações. Estes incluem adicionar uma camada extra de autenticação, e manter um intervalo mínimo de retenção para fins de recuperação.

### <a name="authentication-to-perform-critical-operations"></a>Autenticação para realizar operações críticas

Como parte da adição de uma camada extra de autenticação para operações críticas, é solicitado a introduzir um PIN de segurança quando executa operações de **Stop Protection com Eliminar dados** e alterar operações de **'Passphrase'.**

> [!NOTE]
>
> Atualmente, o pino de segurança não é suportado para a Proteção de **Paragens com dados de eliminação** de DPM e MABS.

Para receber este PIN:

1. Inicie sessão no Portal do Azure.
2. Navegue no cofre de > **Definições** de  **Definições** > Propriedades dos Serviços de **Recuperação** .
3. Em **'Pin' de segurança,** clique em **Gerar**. Isto abre uma lâmina que contém o PIN a ser introduzido na interface de utilizador do agente de recuperação do Azure.
    Este PIN é válido por apenas cinco minutos, e é gerado automaticamente após esse período.

### <a name="maintain-a-minimum-retention-range"></a>Manter uma gama mínima de retenção

Para garantir que há sempre um número válido de pontos de recuperação disponíveis, foram adicionados os seguintes controlos:

- Para a retenção diária, deve ser feito um mínimo de **sete** dias de retenção.
- Para a retenção semanal, deve ser feita uma noção mínima de **quatro** semanas de retenção.
- Para a retenção mensal, deve ser feito um mínimo de **três** meses de retenção.
- Para a retenção anual, deve ser feito um mínimo de **um** ano de retenção.

## <a name="notifications-for-critical-operations"></a>Notificações para operações críticas

Normalmente, quando uma operação crítica é realizada, o administrador de subscrição é enviado uma notificação por e-mail com detalhes sobre a operação. Pode configurar destinatários adicionais de e-mail para estas notificações utilizando o portal Azure.

As características de segurança mencionadas neste artigo fornecem mecanismos de defesa contra ataques direcionados. Mais importante ainda, se um ataque acontecer, estas funcionalidades dão-lhe a capacidade de recuperar os seus dados.

## <a name="troubleshooting-errors"></a>Resolução de erros

| Operação | Detalhes do erro | Resolução |
| --- | --- | --- |
| Mudança de política |A política de apoio não podia ser modificada. Erro: A operação atual falhou devido a um erro de serviço interno [0x29834]. Por favor, tente novamente a operação depois de algum tempo. Se o problema persistir, contacte o suporte da Microsoft. |**Causa:**<br/>Este erro surge quando as definições de segurança estão ativadas, tenta reduzir o intervalo de retenção abaixo dos valores mínimos acima especificados e encontra-se na versão não suportada (as versões suportadas são especificadas na primeira nota deste artigo). <br/>**Ação Recomendada:**<br/> Neste caso, deverá fixar o período de retenção acima do período mínimo de retenção especificado (sete dias para o dia, quatro semanas semanais, três semanas para o mês ou um ano para o ano) para proceder a atualizações relacionadas com as políticas. Opcionalmente, a abordagem preferida seria atualizar o agente de backup, o Azure Backup Server e/ou o DPM UR para alavancar todas as atualizações de segurança. |
| Alterar a frase-passe |O PIN de segurança introduzido está incorreto. (ID: 100130) Forneça o PIN de Segurança correto para completar esta operação. |**Causa:**<br/> Este erro surge quando introduz PIN de Segurança inválido ou expirado enquanto executa o funcionamento crítico (como alterar a frase de passe). <br/>**Ação Recomendada:**<br/> Para completar a operação, deve introduzir PIN de Segurança válido. Para obter o PIN, inscreva-se no portal Azure e navegue para o cofre > Definições > Propriedades > Gere o PIN de Segurança. Utilize este PIN para alterar a frase-passe. |
| Alterar a frase-passe |A operação falhou. ID: 120002 |**Causa:**<br/>Este erro surge quando as definições de segurança estão ativadas, tenta alterar a palavra-passe e encontra-se na versão não suportada (versões válidas especificadas na primeira nota deste artigo).<br/>**Ação Recomendada:**<br/> Para alterar a frase de passe, tem primeiro de atualizar o agente de backup para a versão mínima 2.0.9052, o servidor de backup Azure para a atualização mínima 1 e/ou DPM para o mínimo DPM 2012 R2 UR12 ou DPM 2016 UR2 (links de descarregamento abaixo), em seguida, introduzir PIN de Segurança válido. Para obter o PIN, inscreva-se no portal Azure e navegue para o cofre > Definições > Propriedades > Gere o PIN de Segurança. Utilize este PIN para alterar a frase-passe. |

## <a name="next-steps"></a>Passos seguintes

- [Inicie-se com](backup-azure-vms-first-look-arm.md) o cofre dos Serviços de Recuperação Azure para ativar estas funcionalidades.
- [Descarregue o mais recente agente](https://aka.ms/azurebackup_agent) dos Serviços de Recuperação do Azure para ajudar a proteger os computadores do Windows e guarde os seus dados de backup contra ataques.
- [Descarregue o mais recente Servidor](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) de Backup Azure para ajudar a proteger as cargas de trabalho e proteja os seus dados de backup contra ataques.
- [Baixe UR12 para System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) ou [descarregue UR2 para System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) para ajudar a proteger as cargas de trabalho e proteger os seus dados de backup contra ataques.
