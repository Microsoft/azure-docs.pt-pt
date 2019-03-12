---
title: Migrar dados do StorSimple 5000-7000 série para o Azure File Sync | Documentos da Microsoft
description: Descreve como migrar os dados da série StorSimple 5000/7000 para o Azure File Sync (AFS).
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
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: 9992faa11585d49d3024e00338b4c1dc09da9195
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726673"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrar dados de séries StorSimple 5000 a 7000 para o Azure File Sync

> [!IMPORTANT]
> 31 de Julho de 2019 a série StorSimple 5000/7000 chegará ao fim de estado do suporte (término do suporte ao). Recomendamos que os clientes de série StorSimple 5000/7000 migrem para uma das alternativas descritas no documento.

Migração de dados é o processo de mover dados de uma localização de armazenamento para outro. Isso envolve fazer uma cópia exata de dados atual de uma organização de um dispositivo para outro dispositivo — preferencialmente, sem interromper ou desativar a aplicativos ativos — e, em seguida, redirecionar atividade (e/s) de todos os de entrada/saída para o novo dispositivo. 

StorSimple 5000 e 7000 dispositivos de armazenamento da série chegará ao fim do serviço em Julho de 2019. Isso implica que Microsoft já não será capaz de dar suporte a hardware e software para a série StorSimple 5000/7000 depois de Julho de 2019. Os clientes que utilizam estes dispositivos devem migrar seus dados do StorSimple para outras soluções de armazenamento híbrido no Azure. Este artigo abrange a migração de dados a partir de um dispositivo da série StorSimple 5000/7000 para o Azure File Sync (AFS).

## <a name="intended-audience"></a>Público-alvo

Este artigo destina-se a profissionais da informação (TI) da tecnologia e os info-trabalhadores responsáveis pela implementação e gestão de dispositivos da série StorSimple 5000/7000 no Datacenter. Os clientes que utilizam os seus dispositivos do StorSimple para cargas de trabalho de servidor de ficheiros (com o Windows Server) podem encontrar este caminho de migração especialmente atraente. Se acredita que os recursos do Azure File Sync funcionam bem para a sua organização, em seguida, este artigo ajuda-o a compreender como mover para essas soluções do StorSimple.

## <a name="migration-considerations"></a>Considerações sobre a migração

Esse processo funciona para os clientes que tem configurado uma partilha de ficheiros do Windows com um volume StorSimple para armazenamento. Migrar dados do StorSimple 5000/7000 para o Azure File Sync envolve a conversão nessa localização de partilha de ficheiros para uma [ponto final de servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) e, em seguida, utilizar localmente anexados unidades como outro ponto final que se tornará, em seguida, a nova localização. 

Enquanto se movimenta para AFS, devem ser considerados os seguintes pontos:

1. Os ficheiros do Azure tem atualmente uma restrição de 5 TB/partilha. Esta restrição pode ser ultrapassada através da utilização do Azure File Sync com dados distribuídos por várias partilhas de ficheiros do Azure. Para obter mais informações, reveja os [padrão de crescimento de dados para a implementação de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Esta migração transfere o conjunto completo de dados principal para um dispositivo no local, como a cópia de dados é feita a partir do dispositivo no local. Certifique-se de que tem largura de banda suficiente para acomodar esta transferência.
3. Este processo não preserva os instantâneos que já foram criados. Ele migra apenas os dados primários. O processo também não preserva os modelos de largura de banda associados ou políticas de cópia de segurança. [Utilize a cópia de segurança do Azure](https://docs.microsoft.com/azure/backup/backup-azure-files) para configurar as políticas de cópia de segurança depois dos dados são migrados no arquivo do Azure de partilha.
4. O StorSimple fornece primeiro hardware de terceiros. No entanto, com o Azure ficheiros/Azure File Sync, estiver a utilizar seu próprio hardware do Windows Server local como o cache local. Tem de se certificar de que tem capacidade de armazenamento suficiente para manter o conjunto de dados da sua preferência local. Para obter mais informações sobre a criação de camadas e o destino de requisito de espaço livre de definição, consulte como [criar um ponto final do servidor ao implementar o Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Reveja os [dos preços do Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/) como varia do StorSimple. AFS não tem a eliminação de duplicados e a compressão, como o StorSimple.

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

Aqui encontrará os pré-requisitos de migração para o seu dispositivo de séries 5000 ou 7000 legado para o Azure File Sync. Antes de começar, certifique-se de que tem:

- Acesso a um StorSimple 5000/7000 série dispositivo em execução software versão v2.1.1.518 ou posterior. Versões anteriores não são suportadas. O canto superior direito da web da interface do Usuário do seu dispositivo StorSimple deverá apresentar a versão do software que está a executar.  
    Se o seu dispositivo não está em execução v2.1.1.518, atualize o seu sistema para a versão mínima necessária. Para obter instruções detalhadas, consulte [actualizar o sistema para v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Verificar a existência de quaisquer tarefas de cópia de segurança ativas em execução no dispositivo de origem. Isto inclui os trabalhos do host de Console de proteção de dados do StorSimple. Aguarde que as tarefas atuais concluir. 
- Acesso a um anfitrião do Windows Server ligada ao seu StorSimple dispositivo da série 5000-7000. O anfitrião deve executar uma versão suportada do Windows Server, tal como descrito em [interoperabilidade do Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- Volumes do StorSimple estão montados no anfitrião e contêm partilhas de ficheiros.
- O anfitrião tem o armazenamento local suficiente para manter seus dados armazenados em cache localmente.
- Acesso de nível de proprietário à subscrição do Azure que irá utilizar para implementar o Azure File Sync. Podem ocorrer problemas ao criar um ponto final da cloud para o seu grupo de sincronização, se não tiver proprietário ou permissões de nível de administrador.
- Acesso a uma [conta de armazenamento para fins gerais v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) com uma partilha de ficheiros do Azure que pretende sincronizar com. Para obter mais informações, veja [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
 - Como [criar uma partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Processo de migração

Migrar dados do StorSimple 5000 a 7000 para AFS é um processo de dois passos:
1.  Replicar os dados do servidor de ficheiros no local em que os volumes do StorSimple estão montados para um serviço ficheiros do Azure partilhar.  A replicação é feita através de um agente AFS que instalar.
2.  Desligar o dispositivo StorSimple. Os discos locais, em seguida, atuam como a cache local.

### <a name="migration-steps"></a>Passos de migração

Execute os seguintes passos para migrar a partilha de ficheiros do Windows configurada nos volumes do StorSimple para uma partilha de sincronização de ficheiros do Azure. 
1.  Efetue estes passos no mesmo anfitrião Windows Server em que os volumes do StorSimple estão montados ou utilizam um sistema diferente. 
    - [Preparar o Windows Server para utilizar com o Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Instalar o agente do Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Implementar o serviço de sincronização de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Registar o Windows Server com o serviço de sincronização de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Criar um grupo de sincronização e um ponto final da cloud](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Grupos de sincronização tem de ser feitas para cada partilha de ficheiros Windows que tem de ser migrados do anfitrião.
    - [Criar um ponto de final de servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Especifique o caminho como o caminho do volume do StorSimple que contém os dados de partilha de ficheiros. Por exemplo, se o volume do StorSimple é a unidade `J`, e seus dados residem em `J:/<myafsshare>`, em seguida, adicionar este caminho como um ponto final do servidor. Deixe o **disposição em camadas** como **desativado**.
2.  Aguarde até que a sincronização de servidor de ficheiros estiver concluída. Para cada servidor num grupo de sincronização de determinado, certifique-se:
    - Os carimbos de data / para a última tentativa de sincronização para carregamento e transferência é recentes.
    - O estado está verde para carregamento e transferência.
    - O **atividade de sincronização** mostra muito poucos ou nenhum arquivo restantes para sincronização.
    - O **ficheiros não sincronizar** é 0 para carregamento e transferência.
    Para obter mais informações sobre o quando a sincronização de servidor estiver concluída, aceda a [resolver problemas relacionados com o Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). A sincronização poderá demorar várias horas ou dias, dependendo do tamanho dos dados e a largura de banda. Quando a sincronização estiver concluída, todos os seus dados estão em segurança na partilha de ficheiros do Azure. 
3.  Vá para as partilhas nos volumes do StorSimple. Selecionar uma partilha, o botão direito do mouse e selecione **propriedades**. Tenha em atenção as permissões de partilha sob **segurança**. Estas permissões tem de ser aplicadas manualmente a partilha de novo no passo posterior.
4.  Dependendo se utilizar o mesmo anfitrião do Windows Server ou outro, as próximas etapas serão diferentes.

    Ignore este passo e vá para o passo seguinte, se estiver a utilizar outro anfitrião Windows Server. Se estiver a utilizar o mesmo servidor de ficheiros do Windows para AFS, agora terá alguns minutos de tempo de inatividade. 
    - **Período de indisponibilidade começa** -eliminar o ponto final de servidor que criou no *passo 1F*. 
    - Crie um novo ponto de final de servidor com o caminho onde pretende que os dados residam daqui para frente.
    - Assim que o ponto final do servidor é apresentado como bom estado de funcionamento (pode demorar alguns minutos), verá os dados nesta localização nova. Agora pode configurar o anfitrião do Windows Server para servir ficheiros a partir desta localização nova. - **Período de indisponibilidade termina**.
5.  Se estiver a utilizar outro servidor de ficheiros do Windows para o Azure File Sync, em seguida, não ocorrerá qualquer período de inatividade. 
    - Adicione outro ponto final do servidor com o caminho do armazenamento local que está preparado para usar como um cache em vez do dispositivo StorSimple. 
    - Será capaz de ver os arquivos no novo servidor dentro de alguns minutos. É livre para a transição do seu dispositivo StorSimple para esta nova localização no anfitrião em qualquer altura.

    > [!TIP] 
    > Considere configurar esta nova partilha de ficheiros com o mesmo nome e o mesmo caminho, como o um it está a substituir, para minimizar a interrupção. Se utilizar o DFS-N, isso pode exigir-lhe efetuar alterações na configuração.
6.  Reconfigurar permissões de partilha indicados em *passo 3*.

Se encontrar algum problema durante a migração de dados, [contacte o suporte da Microsoft](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Passos Seguintes

Se AFS não é a solução certa para si, saiba como [migrar dados do StorSimple 5000-7000 série para um dispositivo da 8000 série](storsimple-8000-migrate-from-5000-7000.md).

