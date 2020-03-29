---
title: Migrar dados da série StorSimple 5000-7000 para o Azure File Sync. Microsoft Docs
description: Descreve como migrar dados da série StorSimple 5000/7000 para o Azure File Sync (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b46e9ee8fc3e14981a01cc2425a8ce55d06c5a9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65150745"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrar dados da série StorSimple 5000-7000 para o Azure File Sync

> [!IMPORTANT]
> Em 9 de julho de 2019, a série StorSimple 5000/7000 chegará ao fim do estatuto de suporte (EOS). Recomendamos que os clientes da série StorSimple 5000/7000 migram para uma das alternativas descritas no documento.

A migração de dados é o processo de deslocação de dados de um local de armazenamento para outro. Isto implica fazer uma cópia exata dos dados atuais de uma organização de um dispositivo para outro dispositivo - de preferência sem perturbar ou desativar aplicações ativas - e, em seguida, redirecionar toda a atividade de entrada/saída (I/O) para o novo dispositivo. 

Os dispositivos de armazenamento da série StorSimple 5000 e 7000 chegarão ao fim do serviço em julho de 2019. Isto implica que a Microsoft deixará de ser capaz de suportar o hardware e software da série StorSimple 5000/7000 após julho de 2019. Os clientes que utilizam estes dispositivos devem migrar os seus dados StorSimple para outras soluções híbridas de armazenamento no Azure. Este artigo abrange a migração de dados de um dispositivo da série StorSimple 5000/7000 para o Azure File Sync (AFS).

## <a name="intended-audience"></a>Público-alvo

Este artigo destina-se a profissionais de tecnologias da informação (TI) e trabalhadores do conhecimento responsáveis pela implementação e gestão de dispositivos da série StorSimple 5000/7000 no centro de dados. Os clientes que utilizam os seus dispositivos StorSimple para cargas de trabalho do servidor de ficheiros (com o Windows Server) podem achar este caminho de migração particularmente apelativo. Se acredita que as funcionalidades do Azure File Sync funcionam bem para a sua organização, então este artigo irá ajudá-lo a entender como se mudar para essas soluções a partir do StorSimple.

## <a name="migration-considerations"></a>Considerações sobre a migração

Este processo funciona para clientes que configuraram uma partilha de ficheiros do Windows utilizando um volume StorSimple para armazenamento. Os dados migradores do StorSimple 5000/7000 para o Azure File Sync envolvem a conversão da localização da partilha de ficheiros para um [Ponto Final do Servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) e, em seguida, usar as unidades ligadas localmente como outro ponto final que se tornará a sua nova localização. 

Ao deslocar-se para a AFS, devem ser considerados os seguintes pontos:

1. A Tualmente, a Azure Files tem uma restrição de 5 TB/share. Esta restrição pode ser ultrapassada utilizando o Azure File Sync com dados espalhados por várias ações do Ficheiro Azure. Para obter mais informações, reveja o padrão de crescimento de [Dados para a implementação de Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Esta migração transfere todo o conjunto de dados primários para um dispositivo no local, uma vez que a cópia de dados é feita a partir do dispositivo no local. Certifique-se de que tem largura de banda suficiente para acomodar esta transferência.
3. Este processo não preserva os instantâneos que já foram criados. Só migra os dados primários. O processo também não preserva os modelos de largura de banda associados ou as políticas de backup. Utilize o [Backup Azure](https://docs.microsoft.com/azure/backup/backup-azure-files) para configurar as políticas de backup após a migração dos dados na parte do Ficheiro Azure.
4. A StorSimple fornece hardware de primeira parte. No entanto, com o Azure Files/Azure File Sync, está a utilizar o seu próprio hardware do Windows Server como cache local. Deve certificar-se de que tem capacidade de armazenamento suficiente para manter o conjunto de dados da sua escolha local. Para obter mais informações sobre o tiering e definição do alvo de espaço livre necessário, reveja como criar um ponto final do [servidor ao implementar o Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Reveja os [preços do Azure File Sync,](https://azure.microsoft.com/pricing/details/storage/files/) uma vez que varia de StorSimple. A AFS não tem desduplicação e compressão como o StorSimple.

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

Aqui encontrará os pré-requisitos de migração para o seu legacy 5000 ou 7000 dispositivo da série para o Azure File Sync. Antes de começar, certifique-se de que tem:

- Acesso a um dispositivo da série StorSimple 5000/7000 que executa a versão v2.1.1.518 ou posterior. Versões anteriores não são suportadas. O canto superior direito do UI web do seu dispositivo StorSimple deve apresentar a versão de software que está a funcionar.  
    Se o seu dispositivo não estiver a funcionar v2.1.1.518, por favor atualize o seu sistema para a versão mínima necessária. Para obter instruções detalhadas, consulte o [upgrade do seu sistema para v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Verifique se existem trabalhos de backup ativos que estejam a funcionar no dispositivo de origem. Isto inclui os trabalhos no anfitrião da Consola de Proteção de Dados StorSimple. Espere que os trabalhos atuais completem. 
- Acesso a um anfitrião do Windows Server ligado ao seu dispositivo da série StorSimple 5000-7000. O anfitrião deve estar a executar uma versão suportada do Windows Server, tal como descrito na [interoperabilidade do Ficheiro Sync do Azure.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)
- Os volumes StorSimple são montados no hospedeiro e contêm partilhas de ficheiros.
- O hospedeiro tem armazenamento local suficiente para guardar os seus dados locais.
- Acesso ao nível do proprietário à subscrição azure que utilizará para implementar o Azure File Sync. Pode experimentar problemas ao criar um ponto final em nuvem para o seu grupo de sincronização se não tiver permissões de nível de proprietário ou administrador.
- Acesso a uma conta de [armazenamento v2 de uso geral](https://docs.microsoft.com/azure/storage/common/storage-account-overview) com uma Partilha de Ficheiros Azure a que pretende sincronizar. Para obter mais informações, veja [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Como criar uma partilha de [ficheiros Azure.](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)

## <a name="migration-process"></a>Processo de migração

Os dados migratórios do StorSimple 5000-7000 para a AFS são um processo em duas etapas:
1.  Replicar os dados do servidor de ficheiros no local onde os volumes StorSimple são montados para uma partilha de Ficheiros Azure.  A replicação é feita através de um agente AFS que instala.
2.  Desligue o dispositivo StorSimple. Os discos locais funcionam como a cache local.

### <a name="migration-steps"></a>Passos de migração

Execute os seguintes passos para migrar a partilha de ficheiros do Windows configurada em volumes StorSimple para uma partilha de Sincronização de Ficheiros Azure. 
1.  Execute estes passos no mesmo anfitrião do Windows Server onde os volumes StorSimple são montados ou utilizam um sistema diferente. 
    - [Prepare o Servidor Windows para utilizar com o Sincronizado de Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Instale o agente Dessincronização de ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Implementar o serviço Desarmação Sincronizado](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Registe o Windows Server com o serviço Desvendado Sincronizado](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Crie um grupo de sincronização e um ponto final](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint)de nuvem. Os grupos de sincronização precisam de ser feitos para cada partilha de ficheiros do Windows que precisa de ser migrada do anfitrião.
    - [Criar um ponto final](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint)do servidor . Especifique o caminho como o caminho do volume StorSimple que contém os dados de partilha de ficheiros. Por exemplo, se o volume StorSimple for acionado `J`, e os seus dados `J:/<myafsshare>`residirem, adicione este caminho como ponto final do servidor. Deixe o **Tiering** como **desativado**.
2.  Aguarde até que a sincronização do servidor de ficheiros esteja completa. Para cada servidor num determinado grupo de sincronização, certifique-se de:
    - Os carimbos de tempo para o Último Sincronia para o upload e o download são recentes.
    - O estado é verde tanto para upload como para download.
    - A **Atividade Sincronizada** mostra muito poucos ou nenhum ficheiro restante para sincronizar.
    - O **Ficheiro Sem Sincronização** é 0 para o upload e o download.
    Para obter mais informações sobre quando a sincronização do servidor estiver completa, vá ao [Troubleshoot Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). A sincronização pode demorar várias horas a dias, dependendo do tamanho dos dados e largura de banda. Uma vez concluída a sincronização, todos os seus dados estão seguros na Partilha de Ficheiros Azure. 
3.  Vá para as ações dos volumes StorSimple. Selecione uma parte, clique à direita e selecione **Propriedades**. Note as permissões de partilha no âmbito da **Segurança**. Estas permissões terão de ser aplicadas manualmente à nova parte no passo posterior.
4.  Dependendo se utiliza o mesmo anfitrião do Windows Server ou outro diferente, os próximos passos serão diferentes.

    Salte este passo e vá para o próximo passo se estiver a utilizar um anfitrião do Windows Server diferente. Se estiver a utilizar o mesmo Servidor de Ficheiros Windows para AFS, irá agora sentir alguns minutos de inatividade. 
    - **Inicia-se** o tempo de inatividade - Elimine o ponto final do servidor que criou no *passo 1F*. 
    - Crie um novo ponto final do servidor com o caminho onde pretende que os dados residam.
    - Uma vez que o ponto final do servidor mostre como Saudável (isto pode demorar alguns minutos), você verá os dados neste novo local. Agora pode configurar o seu anfitrião do Windows Server para servir ficheiros a partir deste novo local. - O tempo de **inatividade termina.**
5.  Se estiver a utilizar outro Servidor de Ficheiros Windows para o Sync de Ficheiros Azure, não irá sofrer qualquer tempo de inatividade. 
    - Adicione outro ponto final do servidor com o caminho do armazenamento local que está preparado para usar como cache em vez do dispositivo StorSimple. 
    - Poderá ver os ficheiros no novo servidor em poucos minutos. É livre de efazer a troca do seu dispositivo StorSimple para este novo local no anfitrião a qualquer momento.

    > [!TIP] 
    > Considere configurar esta nova partilha de ficheiros com o mesmo nome e mesmo caminho que aquele que está a substituir, para minimizar a perturbação. Se utilizar o DFS-N, isto poderá exigir que faça alterações na configuração.
6.  Reconfigurar as permissões de partilha conforme se nota no *passo 3*.

Se tiver algum problema durante a migração de dados, [contacte](storsimple-8000-contact-microsoft-support.md)o Suporte da Microsoft . 



## <a name="next-steps"></a>Passos seguintes

Se o AFS não for a solução certa para si, aprenda a [migrar dados de uma série StorSimple 5000-7000 para um dispositivo da série 8000](storsimple-8000-migrate-from-5000-7000.md).

