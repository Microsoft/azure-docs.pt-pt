---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 31e61069c95be9bd1c7a684bb83ebcd93bcb14be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019154"
---
1. Inicie o UnifiedSetup.exe do Azure Site Recovery
2. Em **Antes de começar**, selecione **Adicionar servidores de processo adicionais para aumentar horizontalmente a implementação**.

   ![Adicionar servidor de processos](./media/site-recovery-add-process-server/ps-page-1.png)

3. Em **Detalhes do Servidor de Configuração**, especifique o endereço IP do Servidor de Configuração e a frase de acesso.

   ![Adicionar servidor de processos 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. Nas **Definições de Internet**, especifique como o Fornecedor em execução no Servidor de Configuração se conecta à Recuperação do Site Azure através da Internet.

   ![Adicionar servidor de processos 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * Se pretender estabelecer ligação com o proxy que está atualmente configurado na máquina, selecione **Ligar com definições de proxy existentes**.
   * Se pretender que o Fornecedor se ligue diretamente, selecione **Ligar diretamente sem um proxy**.
   * Se o seu proxy existente requer autenticação ou pretende utilizar um proxy personalizado para a ligação do Fornecedor, selecione **Ligar com definições de proxy personalizado**.

     * Se utilizar um proxy personalizado, terá de especificar o endereço, a porta e as credenciais.
     * Se estiver a utilizar um proxy, já deverá ter acesso permitido nos URLs de serviço.

5. Em **Verificação de Pré-requisitos**, a Configuração executa uma verificação para se certificar de que a instalação pode ser executada. Se for apresentado um aviso sobre a **Verificação de sincronização de hora global**, certifique-se de que a hora no relógio do sistema (definições de **Data e Hora**) é a mesma que o fuso horário.

     ![Adicionar servidor de processos 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. Em **Detalhes do ambiente**, selecione se pretende replicar VMs do VMware. Se pretender, a configuração verifica se o PowerCLI 6.0 está instalado.

     ![Adicionar servidor de processos 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. Em **Localização de Instalação**, selecione onde pretende instalar os binários e armazenar a cache. A unidade que selecionar tem de ter, pelo menos, 5 GB de espaço disponível no disco, mas recomenda-se uma unidade de cache com, pelo menos, 600 GB de espaço livre.
     ![Screenshot que mostra o local de instalação para os binários e armazenamento de cache.](./media/site-recovery-add-process-server/ps-page-6.png)

8. Em **Seleção de Rede**, especifique o serviço de escuta (placa de rede e porta SSL) no qual o Servidor de Configuração envia e recebe dados de replicação. A porta 9443 é a porta predefinida utilizada para envio e receção de tráfego de replicação, mas pode modificar este número de porta para melhor corresponda às necessidades do seu ambiente. Além da porta 9443, podemos também abrir a porta 443, que é utilizada por um servidor Web para orquestrar operações de replicação. Não utilize a Porta 443 para enviar ou receber tráfego de replicação.

     ![Adicionar servidor de processos 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. Em **Resumo**, consulte as informações e clique em **Instalar**. Quando a instalação estiver concluída, é gerada uma frase de acesso. Irá precisar dela, quando ativar a replicação, por isso, copie-a e mantenha-a numa localização segura.

     ![Adicionar servidor de processos 7](./media/site-recovery-add-process-server/ps-page-8.png)
