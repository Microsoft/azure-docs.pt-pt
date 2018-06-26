---
title: Resolver problemas da Cópia de Segurança das Partilhas de Ficheiros do Azure
description: Este artigo apresenta informações sobre a resolução de problemas que ocorrem ao proteger as suas partilhas de ficheiros do Azure.
services: backup
ms.service: backup
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 797637fbaaeb0577d0437f32d4ce244a738be84b
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287333"
---
# <a name="troubleshoot-problems-backing-up-azure-file-shares"></a>Resolução de problemas da cópia de segurança de Partilhas de Ficheiros do Azure
Pode resolver problemas e erros encontrados ao utilizar a cópia de segurança de Partilhas de Ficheiros do Azure com as informações listadas nas tabelas seguintes.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitações da cópia de segurança da partilha de ficheiros do Azure durante a Pré-visualização
A cópia de segurança de Partilhas de ficheiros do Azure está em Pré-visualização. Os seguintes cenários de cópia de segurança não são suportados nas partilhas de ficheiros do Azure:
- Não pode proteger partilhas de ficheiros do Azure em Contas de Armazenamento com a replicação* do [armazenamento georredundante com acesso de leitura](../storage/common/storage-redundancy-grs.md) (RA-GRS).
- Não pode proteger partilhas de ficheiros do Azure em contas de armazenamento que têm Redes Virtuais ou Firewall ativadas.
- Não existe PowerShell ou CLI disponível para proteger Ficheiros do Azure com o Azure Backup.
- O número máximo de cópias de segurança agendadas por dia é de um.
- O número máximo de cópias de segurança a pedido por dia é de quatro.
- Utilize os [bloqueios de recursos](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) da conta de armazenamento para impedir a eliminação acidental de cópias de segurança do seu cofre dos Serviços de Recuperação.
- Não elimine os instantâneos criados pelo Azure Backup. A eliminação de instantâneos pode resultar na perda de pontos de recuperação e/ou falhas de restauro.

\*Partilhas de Ficheiros do Azure em Contas de Armazenamento com a função de [replicação* do armazenamento georredundante](../storage/common/storage-redundancy-grs.md) com acesso de leitura (RA-GRS) como GRS e cobrada a preços de GRS

A cópia de segurança de Partilhas de Ficheiros do Azure em Contas de Armazenamento com replicação de [armazenamento com redundância de zona](../storage/common/storage-redundancy-zrs.md) (ZRS) está atualmente disponível apenas nos E.U.A. Central (CUS) e nos E.U.A. Leste 2 (EUS2)

## <a name="configuring-backup"></a>Configurar a Cópia de Segurança
A tabela seguinte apresenta informações para a configuração da cópia de segurança:

| Configurar a Cópia de Segurança | Sugestões de Solução ou Resolução |
| ------------------ | ----------------------------- |
| Não consegui encontrar a minha Conta de Armazenamento para configurar a cópia de segurança da partilha de ficheiros do Azure | <ul><li>Aguarde até que a deteção esteja concluída. <li>Verifique se alguma Partilha de ficheiros na conta de armazenamento já está protegida por outro Cofre de Serviços de Recuperação. **Nota**: as partilhas de ficheiros numa Contas de Armazenamento apenas podem ser protegidas num único cofre dos Serviços de Recuperação. <li>Garanta que a Partilha de ficheiros não está presente em qualquer uma das Contas de Armazenamento não suportadas.|
| Um erro no portal indica uma falha na deteção das contas de armazenamento. | Se a sua subscrição for parceiro (compatível com o CSP), ignore o erro. Se a sua subscrição não for compatível com o CSP e as contas de armazenamento não puderem ser detetadas, contacte o suporte.|
| Falha no registo ou na validação da Conta de Armazenamento selecionada.| Repita a operação. Se o problema persistir, contacte o suporte.|
| Não foram listadas nem localizadas Partilhas de ficheiros na Conta de Armazenamento selecionada. | <ul><li> Verifique se a Conta de Armazenamento existe no Grupo de Recursos (e se não foi eliminada ou movida após o último registo/validação no cofre).<li>Verifique se a Partilha de ficheiros que procura proteger não foi eliminada. <li>Verifique se a Conta de Armazenamento é suportada para a cópia de segurança da Partilha de ficheiros.<li>Verifique se a partilha de Ficheiros já está protegida no mesmo cofre dos Serviços de Recuperação.|
| A configuração da Partilha de ficheiros da cópia de segurança (ou a configuração da política de proteção) está a falhar. | <ul><li>Repita a operação para ver se o problema persiste. <li> Verifique se a Partilha de ficheiros que quer proteger não foi eliminada. <li> Se estiver a tentar proteger várias Partilhas de ficheiros em simultâneo e algumas das partilhas de ficheiros estiverem a falhar, repita a configuração da cópia de segurança para as Partilhas de ficheiros com falhas. |
| Não é possível eliminar o cofre dos Serviços de Recuperação após desproteger uma Partilha de ficheiros. | No portal do Azure, abra o seu Cofre > **Infraestrutura de Cópia de Segurança** > **Contas de armazenamento** e clique em **Anular o Registo** para remover a conta de armazenamento do cofre dos Serviços de Recuperação.|


## <a name="error-messages-for-backup-or-restore-job-failures"></a>Mensagens de erro para falhas de Tarefas de Cópia de Segurança ou de Restauro

| Mensagens de erro | Sugestões de Solução ou Resolução |
| -------------- | ----------------------------- |
| A operação falhou uma vez que a Partilha de ficheiros não foi encontrada. | Verifique se a Partilha de ficheiros que procura proteger não foi eliminada.|
| A conta de armazenamento não foi encontrada ou não é suportada. | <ul><li>Verifique se a conta de armazenamento existe no Grupo de Recursos e se não foi eliminada ou removida do Grupo de Recursos após a última validação. <li> Verifique se a Conta de armazenamento é suportada para a cópia de segurança da Partilha de ficheiros.|
| Atingiu o limite máximo de instantâneos para esta partilha de ficheiros. Poderá realizar mais quando os antigos expirarem. | <ul><li> Este erro pode ocorrer quando cria várias cópias de segurança a pedido de um Ficheiro. <li> Há um limite de 200 instantâneos por Partilha de ficheiros, incluindo os realizados pelo Azure Backup. As cópias de segurança (ou instantâneos) agendadas mais antigas são limpas automaticamente. As cópias de segurança a pedido (ou instantâneos) têm de ser eliminadas, caso seja atingido o limite máximo.<li> Elimine as cópias de segurança a pedido (instantâneos de partilhas de ficheiros do Azure) do portal de Ficheiros do Azure. **Nota**: perde os pontos de recuperação se eliminar instantâneos criados pelo Azure Backup. |
| A cópia de segurança ou o restauro da partilha de ficheiros falhou devido à limitação do serviço de armazenamento. Tal poderá ocorrer porque o serviço de armazenamento está ocupado a processar outros pedidos para a conta de armazenamento em questão.| Repita a operação após algum tempo. |
| Falha no restauro devido a Partilha de Ficheiros de Destino Não Encontrada. | <ul><li>Verifique se a Conta de Armazenamento selecionada existe e se a partilha de Ficheiros de Destino não foi eliminada. <li> Verifique se a Conta de Armazenamento é suportada para a cópia de segurança da Partilha de ficheiros. |
| De momento, o Azure Backup não é suportado para Partilhas de Ficheiros do Azure em Contas de Armazenamento com Redes Virtuais ativadas. | Desative as Redes Virtuais na Conta de Armazenamento para garantir operações de restauro ou cópias de segurança com êxito. |
| As tarefas de Cópia de Segurança ou Restauro falharam porque a conta de armazenamento está no estado Bloqueado. | Remova o bloqueio da Conta de Armazenamento ou utilize o bloqueio de eliminação em vez do bloqueio de leitura e repita a operação. |
| A recuperação falhou porque o número de ficheiros com falhas é superior ao limiar. | <ul><li> Os motivos das falhas de recuperação são apresentados num ficheiro (o caminho é indicado nos detalhes da Tarefa). Resolva as falhas e repita a operação de restauro apenas para os ficheiros com falhas. <li> Motivos comuns para as falhas do Restauro de ficheiros: <br/> - Verifique se os ficheiros com falhas não estão atualmente em utilização. <br/> - Existe um diretório com o mesmo nome do ficheiro com falhas no diretório principal. |
| A recuperação falhou uma vez que não foi possível recuperar nenhum ficheiro. | <ul><li> Os motivos das falhas de recuperação são apresentados num ficheiro (o caminho é indicado nos detalhes da Tarefa). Resolva as falhas e repita a operação de restauro apenas para os ficheiros com falhas. <li> Motivos comuns para as falhas do restauro de ficheiros: <br/> - Verifique se os ficheiros com falhas não estão atualmente em utilização. <br/> - Existe um diretório com o mesmo nome do ficheiro com falhas no diretório principal. |
| O restauro falha porque um dos ficheiros na origem não existe. | <ul><li> Os itens selecionados não estão presentes nos dados do ponto de recuperação. Para recuperar os ficheiros, forneça a lista de ficheiros correta. <li> O instantâneo da partilha de ficheiros que corresponde ao ponto de recuperação foi eliminado manualmente. Selecione outro ponto de recuperação e repita a operação de restauro. |
| Está em curso uma tarefa de Recuperação para o mesmo destino. | <ul><li>A cópia de segurança da partilha de ficheiros não suporta a recuperação paralela para a mesma Partilha de ficheiros de destino. <li>Aguarde que a recuperação existente termine e tente novamente. Se não encontrar uma tarefa de recuperação no cofre dos Serviços de Recuperação, verifique nos outros cofres de Serviços de Recuperação na mesma subscrição. |
| A operação de restauro falhou uma vez que a partilha de ficheiros de destino está cheia. | Aumente a quota de tamanho da partilha de ficheiros de destino para acomodar os dados de restauro e repita a operação. |
| A operação de restauro falhou porque ocorreu um erro ao executar as operações de pré-restauro nos recursos do Serviço de Sincronização de Ficheiros associados à partilha de ficheiros de destino. | Tente novamente após algum tempo. Se o problema persistir, contacte o suporte da Microsoft. |
| Não foi possível recuperar com êxito um ou mais ficheiros. Para obter mais informações, verifique a lista de ficheiros com falhas no caminho indicado acima. | <ul> <li> Os motivos das falhas de recuperação são apresentados no ficheiro (o caminho é indicado nos detalhes da Tarefa), resolva as falhas e repita a operação de restauro apenas para os ficheiros com falhas. <li> Motivos comuns para as falhas do restauro de ficheiros: <br/> - Verifique se os ficheiros com falhas não estão atualmente em utilização. <br/> - Existe um diretório com o mesmo nome dos ficheiros com falhas no diretório principal. |

## <a name="see-also"></a>Consulte também
Para obter mais informações sobre a cópia de segurança das partilhas de ficheiros do Azure, veja:
- [Fazer cópia de segurança das partilhas de ficheiros do Azure](backup-azure-files.md)
- [FAQ sobre a cópia de segurança das partilhas de ficheiros do Azure](backup-azure-files-faq.md)
