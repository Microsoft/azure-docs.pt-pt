---
title: Funcionalidades de segurança que protegem backups híbridos
description: Saiba como usar funcionalidades de segurança no Azure Backup para tornar as cópias de segurança mais seguras
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 8c671b1b54b937f518f7179bb6940f31a28a78d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94841023"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funcionalidades de segurança para ajudar a proteger backups híbridos que usam backup Azure

As preocupações com questões de segurança, como malware, ransomware e intrusão, estão a aumentar. Estas questões de segurança podem ser dispendiosas, tanto em termos de dinheiro como de dados. Para evitar tais ataques, o Azure Backup agora fornece funcionalidades de segurança para ajudar a proteger os backups híbridos. Este artigo cobre como ativar e utilizar estas funcionalidades, utilizando um agente dos Serviços de Recuperação Azure e o Azure Backup Server. Essas funcionalidades incluem:

- **Prevenção**. Uma camada adicional de autenticação é adicionada sempre que é realizada uma operação crítica como alterar uma palavra-passe. Esta validação destina-se a garantir que tais operações só podem ser realizadas por utilizadores que tenham credenciais Azure válidas.
- **Alertando**. Uma notificação por e-mail é enviada para o administrador de subscrição sempre que uma operação crítica como a eliminação de dados de backup é realizada. Este e-mail garante que o utilizador é notificado rapidamente sobre tais ações.
- **Recuperação**. Os dados de cópia de segurança eliminados são retidos por mais 14 dias a contar da data da eliminação. Isto garante a recuperabilidade dos dados dentro de um determinado período de tempo, por isso não há perda de dados mesmo que um ataque aconteça. Além disso, um maior número de pontos mínimos de recuperação são mantidos para proteger contra dados corruptos.

> [!NOTE]
> As funcionalidades de segurança não devem ser ativadas se estiver a utilizar a infraestrutura como cópia de segurança vM de serviço (IaaS). Estas funcionalidades ainda não estão disponíveis para a cópia de segurança da IaaS VM, pelo que permitir-lhes não terá qualquer impacto. As funcionalidades de segurança só devem ser ativadas se estiver a utilizar: <br/>
>
> - **Agente Azure Backup**. Agente mínimo versão 2.0.9052. Depois de ter ativado estas funcionalidades, deverá atualizar para esta versão do agente para realizar operações críticas. <br/>
> - **Servidor de backup Azure**. Versão 2.0.9052 do agente de backup Azure com atualização do Servidor de Backup Azure 1. <br/>
> - **Gestor de Proteção de Dados do Centro de Sistema**. Mínimo Azure Backup agente versão 2.0.9052 com Data Protection Manager 2012 R2 UR12 ou Data Protection Manager 2016 UR2. <br/>

> [!NOTE]
> Estas funcionalidades estão disponíveis apenas para o cofre dos Serviços de Recuperação. Todos os recém-criados cofres dos Serviços de Recuperação têm estas funcionalidades ativadas por padrão. Para os cofres dos Serviços de Recuperação existentes, os utilizadores permitem estas funcionalidades utilizando os passos mencionados na secção seguinte. Depois de as funcionalidades estarem ativadas, aplicam-se a todos os computadores do agente recovery Services, às instâncias do Servidor de Backup Azure e aos servidores do Data Protection Manager registados no cofre. Ativar esta definição é uma ação única e não é possível desativar estas funcionalidades depois de as ativar.
>

## <a name="enable-security-features"></a>Ativar funcionalidades de segurança

Se estiver a criar um cofre dos Serviços de Recuperação, pode utilizar todas as funcionalidades de segurança. Se estiver a trabalhar com um cofre existente, ative as funcionalidades de segurança seguindo estes passos:

1. Inscreva-se no portal Azure utilizando as suas credenciais Azure.
2. Selecione Procurar e escrever **Serviços** **de Recuperação**.

    ![Screenshot do portal Azure Navegue opção](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    É apresentada a lista dos cofres dos Serviços de Recuperação. A partir desta lista, selecione um cofre. O dashboard do cofre selecionado é aberto.
3. Da lista de itens que aparecem debaixo do cofre, em **Definições**, selecione **Properties**.

    ![Screenshot das opções de cofre dos Serviços de Recuperação](./media/backup-azure-security-feature/vault-list-properties.png)
4. Em **Definições de Segurança**, selecione **Update**.

    ![Screenshot das propriedades do cofre dos Serviços de Recuperação](./media/backup-azure-security-feature/security-settings-update.png)

    O link de atualização abre o painel **de Definições de Segurança,** que fornece um resumo das funcionalidades e permite-lhe ative-las.
5. Da lista de drop-down **já configuraste a autenticação multi-factor Azure AD?** [](../active-directory/authentication/concept-mfa-howitworks.md) Se estiver ativado, é-lhe pedido que autente para autenticar a partir de outro dispositivo (por exemplo, um telemóvel) enquanto faz a sessão no portal Azure.

   Quando executa operações críticas em Backup, tem de introduzir um PIN de segurança, disponível no portal Azure. Ativar a autenticação multi-factor Azure AD adiciona uma camada de segurança. Apenas os utilizadores autorizados com credenciais Azure válidas, e autenticados a partir de um segundo dispositivo, podem aceder ao portal Azure.
6. Para guardar as definições de segurança, selecione **Ative** e selecione **Guardar**. Pode selecionar **Ativar** apenas depois de selecionar um valor da lista **de autenticação multi-factor Ad Azure configurada no** passo anterior?

    ![Screenshot das definições de segurança](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Recuperar dados de backup eliminados

A cópia de segurança retém dados de cópias de segurança eliminados por mais 14 dias e não o elimina imediatamente se for realizada a cópia de segurança stop com a operação **de dados de backup.** Para restaurar estes dados no período de 14 dias, tome os seguintes passos, dependendo do que está a usar:

Para os utilizadores **de agentes dos Serviços de Recuperação da Azure:**

1. Se o computador onde estavam a ocorrer cópias de segurança ainda estiver disponível, reproteja as fontes de dados eliminadas e utilize os [dados de Recuperação para a mesma máquina](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) nos Serviços de Recuperação do Azure, para recuperar de todos os antigos pontos de recuperação.
2. Se este computador não estiver disponível, utilize [a Recover para uma máquina alternativa](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) para utilizar outro computador Azure Recovery Services para obter estes dados.

Para os utilizadores **do Servidor de Backup Azure:**

1. Se o servidor onde estavam a acontecer cópias de segurança ainda estiver disponível, reprotete as fontes de dados eliminadas e utilize a funcionalidade **'Recuperar Dados'** para recuperar de todos os antigos pontos de recuperação.
2. Se este servidor não estiver disponível, utilize [os dados de Recuperação de outro Servidor de Backup Azure](backup-azure-alternate-dpm-server.md) para utilizar outra instância do Azure Backup Server para obter estes dados.

Para os utilizadores **do Gestor de Proteção de Dados:**

1. Se o servidor onde estavam a acontecer cópias de segurança ainda estiver disponível, reprotete as fontes de dados eliminadas e utilize a funcionalidade **'Recuperar Dados'** para recuperar de todos os antigos pontos de recuperação.
2. Se este servidor não estiver disponível, utilize [o Add External DPM](backup-azure-alternate-dpm-server.md) para utilizar outro servidor do Data Protection Manager para obter estes dados.

## <a name="prevent-attacks"></a>Prevenir ataques

Foram adicionadas verificações para garantir que apenas utilizadores válidos podem realizar várias operações. Estes incluem a adição de uma camada extra de autenticação, e a manutenção de um intervalo mínimo de retenção para fins de recuperação.

### <a name="authentication-to-perform-critical-operations"></a>Autenticação para realização de operações críticas

Como parte da adição de uma camada extra de autenticação para operações críticas, é solicitado que introduza um PIN de segurança quando efetuar **a Proteção stop com as** operações de Eliminação de dados e Alteração **Passphrase.**

> [!NOTE]
>
> Atualmente, o pin de segurança não é suportado para **parar a proteção com os dados de Eliminação** de DPM e MABS.

Para receber este PIN:

1. Inicie sessão no portal do Azure.
2. Navegue para o **cofre dos serviços de recuperação**  >  **Propriedades de**  >  **configurações**.
3. Em **Pin de segurança,** selecione **Gerar**. Isto abre um painel que contém o PIN a ser introduzido na interface de utilizador do agente Azure Recovery Services.
    Este PIN é válido por apenas cinco minutos, e é gerado automaticamente após esse período.

### <a name="maintain-a-minimum-retention-range"></a>Manter um intervalo mínimo de retenção

Para garantir que existem sempre um número válido de pontos de recuperação disponíveis, foram adicionados os seguintes controlos:

- Para a retenção diária, deve ser feito um mínimo de **sete** dias de retenção.
- Para a retenção semanal, deve ser feita uma retenção mínima de **quatro** semanas.
- Para a retenção mensal, deve ser feito um mínimo de **três** meses de retenção.
- Para a retenção anual, deve ser feito um mínimo de **um** ano de retenção.

## <a name="notifications-for-critical-operations"></a>Notificações para operações críticas

Normalmente, quando uma operação crítica é realizada, o administrador de subscrição é enviado uma notificação de e-mail com detalhes sobre a operação. Pode configurar destinatários de e-mail adicionais para estas notificações utilizando o portal Azure.

Os recursos de segurança mencionados neste artigo fornecem mecanismos de defesa contra ataques direcionados. Mais importante ainda, se um ataque acontecer, estas funcionalidades dão-lhe a capacidade de recuperar os seus dados.

## <a name="troubleshooting-errors"></a>Resolução de erros

| Operação | Detalhes do erro | Resolução |
| --- | --- | --- |
| Mudança de política |A política de apoio não pôde ser modificada. Erro: A operação atual falhou devido a um erro de serviço interno [0x29834]. Por favor, recaia a operação depois de algum tempo. Se o problema persistir, contacte o Suporte da Microsoft. |**Causa:**<br/>Este erro aparece quando as definições de segurança estão ativadas, tenta reduzir o intervalo de retenção abaixo dos valores mínimos acima especificados e está numa versão não suportada (as versões suportadas são especificadas na primeira nota deste artigo). <br/>**Ação Recomendada:**<br/> Neste caso, deverá definir o período de retenção acima do período mínimo de retenção especificado (sete dias para o dia-a-dia, quatro semanas para semanalmente, três semanas para mensal ou um ano para o ano) para proceder com atualizações relacionadas com a política. Opcionalmente, uma abordagem preferida seria atualizar o agente de backup, O Azure Backup Server e/ou DPM UR para alavancar todas as atualizações de segurança. |
| Alterar a frase de passe |PIN de segurança introduzido está incorreto. (ID: 100130) Forneça o PIN de segurança correto para completar esta operação. |**Causa:**<br/> Este erro ocorre quando introduz PIN de segurança inválido ou expirado durante a operação crítica (como alterar a palavra-passe). <br/>**Ação Recomendada:**<br/> Para completar a operação, tem de introduzir PIN de segurança válido. Para obter o PIN, inscreva-se no portal Azure e navegue para o cofre dos Serviços de Recuperação > Definições > Propriedades > Gerar PIN de Segurança. Utilize este PIN para alterar a palavra-passe. |
| Alterar a frase de passe |A operação falhou. ID: 120002 |**Causa:**<br/>Este erro aparece quando as definições de segurança estão ativadas, tenta alterar a palavra-passe e está numa versão não suportada (versões válidas especificadas na primeira nota deste artigo).<br/>**Ação Recomendada:**<br/> Para alterar a palavra-passe, tem primeiro de atualizar o agente de backup para a versão mínima 2.0.9052, Azure Backup Server para atualização mínima 1 e/ou DPM para o mínimo DPM 2012 R2 UR12 ou DPM 2016 UR2 (links de descarregamento abaixo), e depois introduzir um PIN de segurança válido. Para obter o PIN, inscreva-se no portal Azure e navegue para o cofre dos Serviços de Recuperação > Definições > Propriedades > Gerar PIN de Segurança. Utilize este PIN para alterar a palavra-passe. |

## <a name="next-steps"></a>Passos seguintes

- [Começa com o cofre dos Serviços de Recuperação Azure](backup-azure-vms-first-look-arm.md) para ativar estas funcionalidades.
- [Descarregue o mais recente agente dos Serviços de Recuperação do Azure](https://aka.ms/azurebackup_agent) para ajudar a proteger os computadores do Windows e guardar os seus dados de backup contra ataques.
- [Descarregue o mais recente Servidor de Backup Azure](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) para ajudar a proteger as cargas de trabalho e proteger os seus dados de backup contra ataques.
- [Baixe UR12 para System Center 2012 R2 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) ou [descarregue UR2 para System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) para ajudar a proteger cargas de trabalho e proteger os seus dados de backup contra ataques.
