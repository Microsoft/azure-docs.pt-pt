---
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 11/09/2018
ms.author: raynew
ms.openlocfilehash: 3631d2e9beaa7c0d9ee018a32981a278381a7d86
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108498"
---
## <a name="defining-a-backup-policy"></a>Definir uma política de cópia de segurança
Uma política de cópia de segurança define uma matriz de quando os instantâneos de dados são executados e quanto tempo os instantâneos são retidos. Ao definir uma política para a cópia de segurança de uma VM, pode acionar uma tarefa de cópia de segurança *uma vez por dia*. Quando cria uma nova política, esta é aplicada ao cofre. A interface da política de cópia de segurança tem o seguinte aspeto:

![Política de cópia de segurança](./media/backup-create-policy-for-vms/backup-policy.png)

Para criar uma política:

1. Introduza um nome para o **Nome da política**.
2. Os instantâneos dos seus dados podem ser efetuados num intervalo Diário ou Semanal. Utilize o menu pendente **Frequência da Cópia de segurança** para escolher se os instantâneos de dados são efetuados num intervalo Diário ou Semanal.

   * Se escolher um intervalo Diário, utilize o controlo realçado para selecionar a hora do dia para o instantâneo. Para alterar a hora, anule a seleção da hora e selecione novamente a hora.

     ![Política de cópia de segurança diária](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>
   * Se escolher um intervalo Semanal, utilize os controlos realçados para selecionar o(s) dia(s) da semana e a hora do dia para efetuar o instantâneo. No menu dos dias, selecione um ou vários dias. No menu das horas, selecione uma hora. Para alterar a hora, anule a seleção da hora e selecione a nova hora.

     ![Política de cópia de segurança semanal](./media/backup-create-policy-for-vms/backup-policy-weekly.png)
3. Por predefinição, todas as opções do **Intervalo de Retenção** estão selecionadas. Desmarque os limites do intervalo de retenção que não pretende utilizar. Em seguida, especifique o intervalo(s) a utilizar.

    Os intervalos de retenção Mensal e Anual permitem-lhe especificar os instantâneos com base num incremento semanal ou diário.

   > [!NOTE]
   > 
   > - Ao proteger uma VM, é executada uma tarefa de cópia de segurança uma vez por dia. A hora quando a cópia de segurança é executada é a mesma para cada intervalo de retenção.
   > - Ponto de recuperação é gerado na data e hora quando o instantâneo de cópia de segurança é concluído seja como for quando a tarefa de cópia de segurança era agenda.
   >   - Ex. Se a frequência de cópia de segurança é agendada às 23:00: 30 e devido a qualquer problema instantâneo for concluído às 12h: 01, mas, em seguida, o ponto de recuperação será criado com a data e 12AM: 01 seguinte.
   > - Em caso de cópia de segurança mensal se a cópia de segurança está definida para ser executado no primeiro dia de cada mês e se o instantâneo é concluído no dia seguinte devido a algum problema, em seguida, o ponto de recuperação criado para cópia de segurança mensal vai ser etiquetado com o dia seguinte (ou seja segundo desse mês).


4. Depois de definir todas as opções para a política, na parte superior do painel, clique em **Guardar**.

    A nova política é imediatamente aplicada ao cofre.
