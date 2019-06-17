---
title: Instalar o adaptador do StorSimple para SharePoint | Documentos da Microsoft
description: Descreve como instalar e configurar ou remover o adaptador do StorSimple para SharePoint num farm de servidores SharePoint.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: v-sharos
ms.openlocfilehash: a2f8e75578e396085e7d80f43c1180e158967061
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60633423"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instalar e configurar o StorSimple Adapter para SharePoint
## <a name="overview"></a>Descrição geral
O StorSimple Adapter para SharePoint é um componente que permite fornecer armazenamento flexíveis do Microsoft Azure StorSimple e proteção de dados para farms de servidores SharePoint. Pode usar o adaptador para mover conteúdo de objeto binário grande (BLOB) das bases de dados de conteúdo do SQL Server para o dispositivo de armazenamento do Microsoft Azure StorSimple hybrid cloud.

O StorSimple Adapter para SharePoint funciona como um fornecedor de armazenamento de BLOBS remoto (RBS) e utiliza a funcionalidade de armazenamento de BLOBS do SQL Server remoto para armazenar o conteúdo do SharePoint não estruturado (na forma de BLOBs) num servidor de ficheiros que é apoiado por um dispositivo StorSimple.

> [!NOTE]
> O StorSimple Adapter para SharePoint suporta o SharePoint Server 2010 Remote BLOB Storage (RBS). Ele não suporta o SharePoint Server 2010 externo BLOB Storage (EBS).


* Para transferir o StorSimple Adapter para SharePoint, aceda a [StorSimple Adapter para SharePoint] [ 1] no Microsoft Download Center.
* Para informações sobre o planeamento de RBS e RBS limitações, aceda a [decidir utilizar RBS no SharePoint 2013] [ 2] ou [planear RBS (SharePoint Server 2010)] [ 3].

O resto desta descrição geral descreve resumidamente a função do StorSimple Adapter para SharePoint e os limites de capacidade e desempenho de SharePoint que deve estar atento antes de instalar e configurar o adaptador. Depois de rever estas informações, aceda a [adaptador do StorSimple para instalação do SharePoint](#storsimple-adapter-for-sharepoint-installation) para iniciar a configuração do adaptador.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Adaptador do StorSimple para os benefícios do SharePoint
No site do SharePoint, conteúdo é armazenado como dados BLOB não estruturados num ou mais bancos de dados. Por predefinição, estas bases de dados estão alojados em computadores que estão a executar o SQL Server e que estão localizados no farm de servidor do SharePoint. BLOBs rapidamente podem aumentar de tamanho, consumindo grandes quantidades de armazenamento no local. Por esse motivo, poderá encontrar a solução de armazenamento de outro, mais econômica. SQL Server fornece uma tecnologia chamada remota Blob Storage (RBS) que lhe permite armazenar o conteúdo do BLOB no sistema de arquivos, fora da base de dados do SQL Server. Com o RBS, BLOBs, podem residir no sistema de ficheiros no computador que está a executar o SQL Server ou podem ser armazenados no sistema de arquivos no outro computador do servidor.

RBS requer que usar um provedor de RBS, por exemplo, o StorSimple Adapter para SharePoint, para ativar o RBS no SharePoint. O StorSimple Adapter para SharePoint funciona com o RBS, que lhe permite mover BLOBs para um servidor de cópia de segurança do sistema do Microsoft Azure StorSimple. O Microsoft Azure StorSimple, em seguida, armazena os dados de BLOBS localmente ou na cloud, com base na utilização. BLOBs que são muito ativos (normalmente referidos como dados de acesso frequente ou de camada 1) residirem localmente. Dados menos ativos e de arquivo de dados reside na cloud. Depois de ativar RBS num banco de dados, qualquer novo conteúdo BLOB criado no SharePoint é armazenado no dispositivo StorSimple e não na base de dados do conteúdo.

A implementação do Microsoft Azure StorSimple de RBS fornece as seguintes vantagens:

* Ao mover conteúdo do BLOB para um servidor separado, pode reduzir a carga de consulta no SQL Server, que pode melhorar a capacidade de resposta do SQL Server. 
* O Azure StorSimple utiliza a eliminação de duplicados e compressão para reduzir o tamanho dos dados.
* O Azure StorSimple fornece proteção de dados na forma de locais e instantâneos de cloud. Além disso, se colocar o banco de dados no dispositivo StorSimple, pode fazer backup do conteúdo da base de dados e os BLOBs em conjunto de forma consistente com a falha. (Mover a base de dados de conteúdo para o dispositivo só é suportado para o dispositivo da série StorSimple 8000. Esta funcionalidade não é suportada para a série de 5000 ou 7000.)
* O Azure StorSimple inclui funcionalidades de recuperação após desastre incluindo ativação pós-falha, a recuperação de ficheiros e de volume (incluindo a recuperação de teste) e restauração rápida de dados.
* Pode usar o software de recuperação de dados, como Kroll Ontrack PowerControls, com instantâneos do StorSimple de dados de BLOBS para efetuar a recuperação ao nível do item de conteúdo do SharePoint. (Este software de recuperação de dados é uma compra separada).
* O StorSimple Adapter para SharePoint se vincula ao portal de Administração Central do SharePoint, permitindo-lhe gerir toda a solução do SharePoint a partir de uma localização central.

Mover o conteúdo do BLOB para o sistema de ficheiros pode fornecer outras reduções de custos e benefícios. Por exemplo, usar RBS pode reduzir a necessidade de armazenamento dispendioso de camada 1 e, uma vez que ele diminui a base de dados de conteúdo, RBS pode reduzir o número de bancos de dados necessários no farm de servidor do SharePoint. No entanto, outros fatores, como limites de tamanho de base de dados e a quantidade de conteúdo não RBS, também pode afetar os requisitos de armazenamento Para obter mais informações sobre os custos e benefícios do uso de RBS, consulte [planear RBS (SharePoint Foundation 2010)] [ 4] e [decidir utilizar RBS no SharePoint 2013] [5].

### <a name="capacity-and-performance-limits"></a>Limites de capacidade e desempenho
Antes de considerá RBS na sua solução do SharePoint, deve estar ciente do desempenho testado e limites de capacidade do SharePoint Server 2010 e SharePoint Server 2013 e como estes limites se relaciona com um desempenho aceitável. Para obter mais informações, consulte [limites de Software e os limites para SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Antes de configurar o RBS, reveja o seguinte:

* Certifique-se de que o tamanho total do conteúdo (o tamanho de um banco de dados de conteúdo) mais o tamanho dos BLOBs externalized associados não excede o limite de tamanho RBS suportado pelo SharePoint. Este limite é de 200 GB. 
  
    **A base de dados de conteúdo de medida e o tamanho do BLOB**
  
  1. Execute esta consulta no WFE de Administração Central. Inicie a Shell de gestão do SharePoint e, em seguida, introduza o seguinte comando do Windows PowerShell para obter o tamanho das bases de dados de conteúdo:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Este passo obtém o tamanho da base de dados de conteúdo no disco.
  2. Execute um dos seguintes consultas SQL no SQL Management Studio na caixa de servidor SQL em cada banco de dados de conteúdo e adicionar o resultado para o número de obtido no passo 1.
     
     No SharePoint 2013 bancos de dados, introduza:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     No SharePoint 2010 bancos de dados, introduza:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Este passo obtém o tamanho dos BLOBs que tenham sido externalized.
* Recomendamos que armazene todos os conteúdos BLOB e a base de dados localmente no dispositivo StorSimple. O dispositivo StorSimple é um cluster de dois nós para elevada disponibilidade. Colocar as bases de dados de conteúdo e os BLOBs no dispositivo StorSimple fornece elevada disponibilidade.
  
    Utilize as tradicionais práticas recomendadas de migração do SQL Server para mover a base de dados de conteúdo para o dispositivo StorSimple. Mova a base de dados apenas depois de todo o conteúdo BLOB da base de dados foi movido para a partilha de ficheiros através de RBS. Se optar por mover a base de dados de conteúdo para o dispositivo StorSimple, recomendamos que configure o armazenamento de base de dados de conteúdo no dispositivo como um volume primário.
* No Microsoft Azure StorSimple, se utilizar volumes em camadas, não há nenhuma forma de garantir que os conteúdos armazenados localmente no StorSimple dispositivo não vai ser colocado em camadas para o armazenamento de nuvem do Microsoft Azure. Por este motivo, recomendamos a utilização de volumes do StorSimple afixado localmente em conjunto com o RBS do SharePoint. Isto irá garantir que todo o conteúdo BLOB permanece localmente no dispositivo StorSimple e não é movido para o Microsoft Azure.
* Se não armazenar as bases de dados de conteúdo no dispositivo StorSimple, utilize tradicionais do SQL Server elevada disponibilidade melhores práticas que suportam RBS. Clustering do SQL Server suporta RBS, ao SQL Server espelhamento não. 

> [!WARNING]
> Se não tiver ativado o RBS, não recomendamos a mover a base de dados de conteúdo para o dispositivo StorSimple. Esta é uma configuração não testada.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>Adaptador do StorSimple para instalação do SharePoint
Antes de poder instalar o adaptador do StorSimple para SharePoint, tem de configurar o dispositivo StorSimple e certifique-se de que o farm de servidores SharePoint e a instanciação do SQL Server cumprem todos os pré-requisitos. Este tutorial descreve os requisitos de configuração, bem como dos procedimentos para instalar e atualizar o adaptador do StorSimple para SharePoint.

## <a name="configure-prerequisites"></a>Configurar os pré-requisitos
Antes de poder instalar o adaptador do StorSimple para SharePoint, certifique-se de que o dispositivo StorSimple, o farm de servidores SharePoint e a instanciação do SQL Server cumprem os seguintes pré-requisitos.

### <a name="system-requirements"></a>Requisitos de sistema
O StorSimple Adapter para SharePoint funciona com o seguinte hardware e software:

* Sistema de operativo suportado – Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2
* Versões suportadas do SharePoint – SharePoint Server 2010 ou o SharePoint Server 2013
* Versões suportadas do SQL Server – SQL Server 2008 Enterprise Edition, o SQL Server 2008 R2 Enterprise Edition ou o SQL Server 2012 Enterprise Edition
* Suporte de dispositivos do StorSimple – série StorSimple 8000, 7000 do StorSimple série ou séries StorSimple 5000.

### <a name="storsimple-device-configuration-prerequisites"></a>Pré-requisitos de configuração de dispositivos do StorSimple
O dispositivo StorSimple é um dispositivo de bloco e como tal requer um servidor de ficheiros no qual os dados podem ser hospedados. Recomendamos que utilize um servidor separado, em vez de um servidor existente do farm do SharePoint. Este servidor de ficheiros tem de ser na mesma rede de área local (LAN) que o computador do SQL Server que aloja as bases de dados de conteúdo.

> [!TIP]
> * Se configurar seu farm do SharePoint para elevada disponibilidade, deve implementar também o servidor de ficheiros de elevada disponibilidade.
> * Se não armazenar o conteúdo da base de dados no dispositivo StorSimple, utilize melhores práticas do tradicional de elevada disponibilidade que suportam RBS. Clustering do SQL Server suporta RBS, ao SQL Server espelhamento não. 


Certifique-se de que o dispositivo StorSimple está configurado corretamente e que os volumes apropriados para suportar a sua implantação do SharePoint estão configurados e acessível a partir do seu computador do SQL Server. Aceda a [implementar no seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md) se ainda não tiver implementado e configurado o dispositivo StorSimple. Tome nota do endereço IP do dispositivo StorSimple; irá precisar dele durante o adaptador do StorSimple para a instalação do SharePoint.

Além disso, certifique-se de que o volume a ser utilizado para externalização BLOB cumpre os seguintes requisitos:

* O volume tem de ser formatado com um tamanho de unidade de alocação de 64 KB.
* O front-end da web (WFE) e servidores de aplicações tem de ser capazes de aceder ao volume através de um caminho de convenção de Nomenclatura Universal (UNC).
* O farm de servidores do SharePoint deve ser configurado para escrita para o volume.

> [!NOTE]
> Depois de instalar e configurar o adaptador, todos os externalização de BLOB tem de aceder através do dispositivo StorSimple (o dispositivo irá apresentar os volumes para o SQL Server e gerir as camadas de armazenamento). Não é possível utilizar quaisquer outros elementos para externalização de BLOBS.


Se planeia utilizar o Snapshot Manager do StorSimple para tirar instantâneos dos dados de BLOBS e a base de dados, certifique-se de que instala o Snapshot Manager do StorSimple no servidor de base de dados para que ele possa utilizar o serviço SQL Writer para implementar o serviço de cópia do Windows Volume sombra (VSS).

> [!IMPORTANT]
> Snapshot Manager do StorSimple não suporta o escritor de VSS do SharePoint e não é possível tirar instantâneos consistentes com aplicações de dados do SharePoint. Num cenário de SharePoint, o Snapshot Manager do StorSimple fornece apenas as cópias de segurança consistentes de falhas.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Pré-requisitos de configuração de farm do SharePoint
Certifique-se de que seu farm de servidores do SharePoint está corretamente configurada, da seguinte forma:

* Certifique-se de que seu farm de servidores do SharePoint está em bom estado de funcionamento e verifique o seguinte:
* Todas as SharePoint WFE e registados no farm de servidores de aplicações estão em execução e podem ser alcançados pelo ping do servidor no qual irá instalar o adaptador do StorSimple para SharePoint.
* O serviço de temporizador do SharePoint (SPTimerV3 ou SPTimerV4) está em execução em cada servidor WFE e o servidor de aplicações.
* O serviço de temporizador do SharePoint e o pool de aplicativos do IIS em que o site de Administração Central do SharePoint está em execução tem privilégios administrativos.
* Certifique-se de que o Internet Explorer Enhanced contexto de segurança (IE ESC) está desativado. Siga estes passos para desativar IE ESC:
  
  1. Feche todas as instâncias do Internet Explorer.
  2. Inicie o Gestor de servidor.
  3. No painel esquerdo, clique em **servidor Local**.
  4. No painel da direita, junto a **configuração de segurança avançada do IE**, clique em **no**.
  5. Sob **administradores**, clique em **desativar**.
  6. Clique em **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Pré-requisitos do BLOB Storage (RBS) remoto
Certifique-se de que está a utilizar uma versão suportada do SQL Server. Apenas as seguintes versões são suportadas e podem utilizar RBS:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Podem ser externalized bLOBs apenas nesses volumes que o dispositivo StorSimple apresenta ao SQL Server. Não existem outros destinos para externalização BLOBS são suportados.

Quando tiver concluído todos os passos de configuração de pré-requisitos, vá para [instalar o adaptador do StorSimple para SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instalar o adaptador do StorSimple para SharePoint
Utilize os seguintes passos para instalar o adaptador do StorSimple para SharePoint. Se estiver a reinstalar o software, consulte [atualizar ou reinstalar o StorSimple Adapter para SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). O tempo necessário para a instalação depende o número total de bases de dados do SharePoint no farm do SharePoint server.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Configurar o RBS
Depois de instalar o adaptador do StorSimple para SharePoint, configure o RBS conforme descrito no procedimento seguinte.

> [!TIP]
> O StorSimple Adapter para SharePoint se conecta a página de Administração Central do SharePoint, permitindo que RBS a ser ativado ou desativado em cada banco de dados de conteúdo no farm do SharePoint. No entanto, ativar ou desativar RBS na base de dados de conteúdos faz com que uma redefinição do IIS, que, consoante a configuração de farm, momentaneamente pode interromper a disponibilidade do SharePoint web front-end (WFE). (Fatores como a utilização de um balanceador de carga de front-end, a atual carga de trabalho do servidor e assim por diante, podem limitar ou eliminar este interrupção). Para proteger os usuários de uma interrupção, é recomendável que ative ou desative RBS apenas durante uma janela de manutenção planeada.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Configurar a recolha de lixo
Quando os objetos são eliminados a partir de um site do SharePoint, eles não são automaticamente eliminados do volume de armazenamento de RBS. Em vez disso, um assíncrona, o programa de manutenção em segundo plano elimina BLOBs órfãos do arquivo de ficheiros. Os administradores de sistema podem agendar este processo seja executado periodicamente ou podem iniciar, sempre que necessário.

Este programa de manutenção (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) é instalado automaticamente em todos os servidores WFE do SharePoint e servidores de aplicações ao ativar a RBS. O programa é instalado na seguinte localização: *unidade de arranque*: \Programas\Microsoft SQL remoto armazenamento de BLOBs 10.50\Maintainer\

Para obter informações sobre como configurar e utilizar o programa de manutenção, consulte [RBS manter no SharePoint Server 2013][8].

> [!IMPORTANT]
> O programa de responsável pela manutenção RBS é utilizar muitos recursos. Deve agendar seja executado apenas durante os períodos de atividade ligeira no farm do SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Eliminar BLOBs órfãos imediatamente
Se tiver de eliminar os BLOBs órfãos imediatamente, pode utilizar as instruções seguintes. Tenha em atenção que estas instruções são um exemplo de como isso pode ser feito num ambiente do SharePoint 2013 com os seguintes componentes:

* O nome do conteúdo da base de dados é WSS_Content.
* O nome do SQL Server é SHRPT13 SQL12\SHRPT13.
* O nome da aplicação web é SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Atualizar ou reinstalar o StorSimple Adapter para SharePoint
Utilize o procedimento seguinte para atualizar o servidor do SharePoint e, em seguida, reinstalar o StorSimple Adapter para SharePoint ou para simplesmente atualizar ou reinstalar o adaptador num farm de servidores do SharePoint existente.

> [!IMPORTANT]
> Antes de tentar atualizar o seu software do SharePoint e/ou a atualização ou reinstalar o StorSimple Adapter para SharePoint, reveja as seguintes informações:
> 
> * Todos os ficheiros que anteriormente foram movidos para o armazenamento externo através de RBS não estarão disponíveis até que a reinstalação estiver concluída e a funcionalidade RBS é ativada novamente. Para limitar o impacto no utilizador, execute qualquer atualização ou reinstalação durante uma janela de manutenção planeada.
> * O tempo necessário para a atualização/reinstalação pode variar, dependendo do número total de bases de dados do SharePoint no farm de servidor do SharePoint.
> * Depois de concluída a atualização/reinstalação, tem de ativar RBS das bases de dados de conteúdo. Ver [RBS configurar](#configure-rbs) para obter mais informações.
> * Se estiver a configurar RBS para um farm do SharePoint que tenha um grande número de bases de dados (mais de 200), o **Administração Central do SharePoint** página poderá tempo limite. Se isso ocorrer, atualize a página. Isto não afeta o processo de configuração.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>Adaptador do StorSimple para remoção do SharePoint
Os procedimentos seguintes descrevem como mover os BLOBs de volta para as bases de dados de conteúdo do SQL Server e, em seguida, desinstalar o adaptador do StorSimple para SharePoint. 

> [!IMPORTANT]
> Tem de mover a BLOBs de volta para as bases de dados de conteúdo, antes de desinstalar o software do adaptador.


### <a name="before-you-begin"></a>Antes de começar
Recolha as seguintes informações antes de mover os dados de volta para as bases de dados de conteúdo do SQL Server e iniciar o processo de remoção do adaptador:

* Os nomes de todas as bases de dados para o qual RBS está ativada
* O caminho UNC do armazenamento de BLOBS configurado

### <a name="move-the-blobs-back-to-the-content-databases"></a>Mover os BLOBs de volta para as bases de dados de conteúdo
Antes de desinstalar o adaptador do StorSimple para o software do SharePoint, é necessário migrar todos os BLOBs que foram externalized novamente para as bases de dados de conteúdo do SQL Server. Se tentar desinstalar o adaptador do StorSimple para SharePoint antes de mover todos os BLOBs novamente para as bases de dados de conteúdo, verá a seguinte mensagem de aviso.

![Mensagem de aviso](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Para mover os BLOBs de volta para as bases de dados de conteúdo
1. Transferir cada um dos objetos externalized.
2. Abra o **Administração Central do SharePoint** página e procure **definições do sistema**.
3. Sob **Azure StorSimple**, clique em **configurar o adaptador do StorSimple**.
4. Na **configurar o adaptador do StorSimple** página, clique no **desativar** botão abaixo de cada um das bases de dados de conteúdo que pretende remover do armazenamento de BLOBS externo. 
5. Elimine os objetos do SharePoint e, em seguida, carregá-los novamente.

Em alternativa, pode utilizar o Microsoft `RBS Migrate()` cmdlet do PowerShell incluído com o SharePoint. Para obter mais informações, consulte [migrar o conteúdo dentro ou fora do RBS](https://technet.microsoft.com/library/ff628255.aspx).

Depois de mover os BLOBs de volta para a base de dados de conteúdo, vá para o passo seguinte: [Desinstalar o adaptador](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Desinstalar o adaptador
Depois de mover os BLOBs de volta para as bases de dados de conteúdo do SQL Server, utilize uma das seguintes opções para desinstalar o adaptador do StorSimple para SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Para utilizar o programa de instalação para desinstalar o adaptador
1. Utilize uma conta com privilégios de administrador para iniciar sessão no servidor web front-end (WFE).
2. Faça duplo clique o StorSimple Adapter para SharePoint installer. O Assistente de configuração é iniciado.
   
    ![Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Clique em **Seguinte**. É apresentada a página seguinte.
   
    ![Remover página do Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Clique em **remover** para selecionar o processo de remoção. É apresentada a página seguinte.
   
    ![Página de confirmação do Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Clique em **remover** para confirmar a remoção. É apresentada a seguinte página de progresso.
   
    ![Página de progresso do Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Quando a remoção estiver concluída, é apresentada a página de conclusão. Clique em **concluir** para fechar o Assistente de configuração.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Para utilizar o painel de controlo para desinstalar o adaptador
1. Abra o painel de controle e, em seguida, clique em **programas e funcionalidades**.
2. Selecione **StorSimple Adapter para SharePoint**e, em seguida, clique em **desinstalação**.

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
