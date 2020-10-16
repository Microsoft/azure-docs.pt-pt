---
title: Migrar dados da série StorSimple 5000-7000 para Azure File Sync Microsoft Docs
description: Descreve como migrar dados da série StorSimple 5000/7000 para Azure File Sync (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 5d656fd8757580b8ce96acf168e92fc847d400ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514071"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrar dados da série StorSimple 5000-7000 para Azure File Sync

> [!IMPORTANT]
> No dia 9 de julho de 2019, a série StorSimple 5000/7000 chegará ao fim do estado de suporte (EOS). Recomendamos que os clientes da série StorSimple 5000/7000 migram para uma das alternativas descritas no documento.

A migração de dados é o processo de deslocação de dados de um local de armazenamento para outro. Isto implica fazer uma cópia exata dos dados atuais de uma organização de um dispositivo para outro - de preferência sem perturbar ou desativar aplicações ativas - e, em seguida, redirecionar toda a atividade de entrada/saída (I/O) para o novo dispositivo. 

Os dispositivos de armazenamento da série StorSimple 5000 e 7000 chegarão ao fim do serviço em julho de 2019. Isto implica que a Microsoft deixará de ser capaz de suportar o hardware e software para a série StorSimple 5000/7000 após julho de 2019. Os clientes que utilizam estes dispositivos devem migrar os seus dados StorSimple para outras soluções de armazenamento híbrido no Azure. Este artigo abrange a migração de dados de um dispositivo da série StorSimple 5000/7000 para Azure File Sync (AFS).

## <a name="intended-audience"></a>Público-alvo

Este artigo destina-se a profissionais de tecnologias da informação (TI) e trabalhadores do conhecimento responsáveis pela implantação e gestão de dispositivos da série StorSimple 5000/7000 no centro de dados. Os clientes que utilizam os seus dispositivos StorSimple para cargas de trabalho do servidor de ficheiros (com o Windows Server) podem achar esta rota de migração particularmente apelativa. Se acredita que as funcionalidades do Azure File Sync funcionam bem para a sua organização, então este artigo irá ajudá-lo a entender como se mover para essas soluções da StorSimple.

## <a name="migration-considerations"></a>Migration considerations (Considerações sobre a migração)

Este processo funciona para clientes que configuraram uma partilha de ficheiros Windows utilizando um volume StorSimple para armazenamento. Os dados migratórios de StorSimple 5000/7000 para Azure File Sync envolvem converter essa localização de partilha de ficheiros para um [Ponto Final do Servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) e, em seguida, usar unidades anexas localmente como outro ponto final que se tornará então a sua nova localização. 

Ao deslocar-se para a AFS, devem ser considerados os seguintes pontos:

1. A Azure Files tem atualmente uma restrição de 5 TB/share. Esta restrição pode ser superada utilizando o Azure File Sync com dados espalhados por várias ações do Azure File. Para obter mais informações, reveja o padrão de crescimento de [dados para a implementação de Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Esta migração descarrega todo o conjunto de dados primários para um dispositivo no local, uma vez que a cópia de dados é feita a partir do dispositivo no local. Certifique-se de que tem largura de banda suficiente para acomodar esta transferência.
3. Este processo não preserva os instantâneos que já foram criados. Só migra os dados primários. O processo também não preserva os modelos de largura de banda associados ou políticas de backup. [Utilize o Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files) para configurar as políticas de backup após a migração dos dados na partilha do Ficheiro Azure.
4. A StorSimple fornece o primeiro hardware de festa. No entanto, com o Azure Files/Azure File Sync, está a utilizar o hardware local do Windows Server como cache local. Deve certificar-se de que tem capacidade de armazenamento suficiente para manter o conjunto de dados da sua escolha local. Para obter mais informações sobre o tiering e definir o alvo de espaço livre necessário, reveja como [criar um ponto final do servidor ao implementar o Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Reveja o [preço do Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/) uma vez que varia de StorSimple. A AFS não tem deduplicação e compressão como a StorSimple.

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

Aqui encontrará os pré-requisitos de migração para o seu legado dispositivo da série 5000 ou 7000 para Azure File Sync. Antes de começar, certifique-se de que tem:

- Acesso a um dispositivo da série StorSimple 5000/7000 com a versão de software v2.1.1.518 ou posterior. Versões anteriores não são suportadas. O canto superior direito da UI web do seu dispositivo StorSimple deve exibir a versão de software que está em execução.  
    Se o seu dispositivo não estiver a funcionar v2.1.1.518, por favor atualize o seu sistema para a versão mínima necessária. Para obter instruções detalhadas, consulte a [Atualização do seu sistema para v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Verifique se existem trabalhos de backup ativos que estejam a ser geridos no dispositivo de origem. Isto inclui os trabalhos no anfitrião da consola de proteção de dados StorSimple. Espere que os trabalhos atuais terminem. 
- Acesso a um anfitrião do Windows Server ligado ao seu dispositivo da série StorSimple 5000-7000. O anfitrião deve estar a executar uma versão suportada do Windows Server, tal como descrito na [interoperabilidade do Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- Os volumes StorSimple são montados no hospedeiro e contêm ações de ficheiro.
- O anfitrião tem armazenamento local suficiente para guardar os seus dados em cache local.
- Acesso ao nível do proprietário à subscrição Azure que utilizará para implementar o Azure File Sync. Pode sentir problemas ao criar um ponto final em nuvem para o seu grupo de sincronização se não tiver permissões de proprietário ou nível de administração.
- Acesso a uma [conta de armazenamento V2 para fins gerais](https://docs.microsoft.com/azure/storage/common/storage-account-overview) com uma Azure File Share a que pretende sincronizar. Para obter mais informações, veja [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Como criar uma partilha de [ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Processo de migração

Os dados migratórios de StorSimple 5000-7000 para AFS é um processo em duas etapas:
1.  Replique os dados do servidor de ficheiros no local onde os volumes StorSimple são montados para uma partilha de Ficheiros Azure.  A replicação é feita através de um agente AFS que instala.
2.  Desligue o dispositivo StorSimple. Os discos locais funcionam então como cache local.

### <a name="migration-steps"></a>Passos de migração

Execute os seguintes passos para migrar a partilha de ficheiros do Windows configurada em volumes StorSimple para uma partilha Azure File Sync. 
1.  Execute estes passos no mesmo anfitrião do Windows Server onde os volumes StorSimple são montados ou utilize um sistema diferente. 
    - [Prepare o Servidor windows para utilizar com o Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Instale o agente Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Implementar o serviço Desincronizador de Armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Registar o Servidor do Windows com o serviço de Sincronização de Armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Crie um grupo de sincronização e um ponto final em nuvem.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint) Os grupos de sincronização precisam de ser feitos para cada partilha de ficheiros do Windows que precisa de ser migrada do hospedeiro.
    - [Criar um ponto final do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Especifique o caminho como o caminho do volume StorSimple que contém os dados de partilha de ficheiros. Por exemplo, se o volume StorSimple estiver a ser acionado `J` , e os seus dados residirem , adicione este caminho como ponto final do `J:/<myafsshare>` servidor. Deixe o **Tiering** como **Desativado**.
2.  Aguarde até que a sincronização do servidor de ficheiros esteja completa. Para cada servidor de um determinado grupo de sincronização, certifique-se de:
    - Os cartões de tempo para o Último Sync tentado para o upload e download são recentes.
    - O estado é verde tanto para o upload como para o download.
    - A **Atividade sincronizada** mostra muito poucos ou nenhum ficheiro restante sincronizado.
    - O **Ficheiros Não Sincronizados** é 0 para o upload e download.
    Para obter mais informações sobre quando a sincronização do servidor estiver completa, aceda ao [Troubleshoot Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). A sincronização pode demorar várias horas a dias, dependendo do tamanho dos dados e largura de banda. Uma vez concluída a sincronização, todos os seus dados estão seguros na Azure File Share. 
3.  Vá às ações dos volumes StorSimple. Selecione uma partilha, clique à direita e selecione **Propriedades.** Note as permissões de partilha em **Segurança.** Estas permissões terão de ser aplicadas manualmente à nova parte no passo posterior.
4.  Dependendo se utiliza o mesmo anfitrião do Windows Server ou outro, os próximos passos serão diferentes.

    Salte este passo e vá para o próximo passo se estiver a utilizar um anfitrião do Windows Server diferente. Se estiver a utilizar o mesmo Servidor de Ficheiros do Windows para AFS, irá agora experimentar alguns minutos de tempo de inatividade. 
    - **Início do tempo de inatividade** - Elimine o ponto final do servidor que criou no *passo 1F*. 
    - Crie um novo ponto final do servidor com o caminho onde pretende que os dados residam em frente.
    - Assim que o ponto final do servidor mostrar como Saudável (isto pode demorar alguns minutos), verá os dados nesta nova localização. Pode agora configurar o anfitrião do Windows Server para servir ficheiros a partir desta nova localização. - **O tempo de inatividade termina.**
5.  Se estiver a utilizar outro Servidor de Ficheiros do Windows para o Azure File Sync, então não experimentará nenhum tempo de inatividade. 
    - Adicione outro ponto final do servidor com o caminho do armazenamento local que está preparado para usar como cache em vez do dispositivo StorSimple. 
    - Em poucos minutos poderá ver os ficheiros do novo servidor. É livre de fazer o interruptor do seu dispositivo StorSimple para este novo local no anfitrião a qualquer momento.

    > [!TIP] 
    > Considere configurar esta nova partilha de ficheiros com o mesmo nome e o mesmo caminho que está a substituir, para minimizar a perturbação. Se utilizar o DFS-N, isto poderá exigir que efete alterações na configuração.
6.  Reconfigure as permissões de partilha como indicado no *passo 3*.

Se tiver algum problema durante a migração de dados, contacte o [Microsoft Support](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Passos seguintes

Se o AFS não for a solução certa para si, aprenda a [migrar dados de uma série StorSimple 5000-7000 para um dispositivo de série 8000](storsimple-8000-migrate-from-5000-7000.md).

