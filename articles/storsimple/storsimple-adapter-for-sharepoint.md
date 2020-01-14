---
title: Instalar o adaptador StorSimple para SharePoint | Microsoft Docs
description: Descreve como instalar e configurar ou remover o adaptador StorSimple para SharePoint em um farm de servidores do SharePoint.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: twooley
ms.openlocfilehash: a841ce8b664389ccd8fdf55de9965f09412fecf5
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930212"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instalar e configurar o adaptador StorSimple para SharePoint
## <a name="overview"></a>Visão geral
O adaptador StorSimple para SharePoint é um componente que permite que você forneça Microsoft Azure StorSimple armazenamento flexível e proteção de dados para farms de servidores do SharePoint. Você pode usar o adaptador para mover conteúdo de BLOB (objeto binário grande) dos bancos de dados de conteúdo SQL Server para o dispositivo de armazenamento de nuvem híbrida Microsoft Azure StorSimple.

O adaptador StorSimple para SharePoint funciona como um provedor de RBS (Remote BLOB Storage) e usa o SQL Server recurso Remote BLOB Storage para armazenar conteúdo não estruturado do SharePoint (na forma de BLOBs) em um servidor de arquivos que é apoiado por um dispositivo StorSimple.

> [!NOTE]
> O adaptador StorSimple para SharePoint dá suporte ao armazenamento de BLOB Remoto (RBS) do SharePoint Server 2010. Ele não dá suporte a EBS (armazenamento de BLOB externo) do SharePoint Server 2010.


* Para baixar o adaptador StorSimple para SharePoint, vá para o [adaptador storsimple para SharePoint][1] no centro de download da Microsoft.
* Para obter informações sobre como planejar as limitações de RBS e RBS, acesse [decidindo usar o RBS no SharePoint 2013][2] ou [planejar o RBS (SharePoint Server 2010)][3].

O restante desta visão geral descreve brevemente a função do adaptador StorSimple para SharePoint e os limites de capacidade e desempenho do SharePoint que você deve conhecer antes de instalar e configurar o adaptador. Depois de examinar essas informações, vá para a [instalação do adaptador do StorSimple para SharePoint](#storsimple-adapter-for-sharepoint-installation) para começar a configurar o adaptador.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Benefícios do adaptador StorSimple para SharePoint
Em um site do SharePoint, o conteúdo é armazenado como dados de BLOB não estruturados em um ou mais bancos de dado de conteúdo. Por padrão, esses bancos de dados são hospedados em computadores que executam o SQL Server e estão localizados no farm de servidores do SharePoint. Os BLOBs podem aumentar rapidamente o tamanho, consumindo grandes quantidades de armazenamento local. Por esse motivo, talvez você queira encontrar outra solução de armazenamento menos cara. O SQL Server fornece uma tecnologia chamada RBS (Remote BLOB Storage) que permite armazenar o conteúdo do BLOB no sistema de arquivos, fora do banco de dados do SQL Server. Com o RBS, os BLOBs podem residir no sistema de arquivos no computador que está executando o SQL Server ou podem ser armazenados no sistema de arquivos em outro computador servidor.

O RBS requer que você use um provedor RBS, como o adaptador StorSimple para SharePoint, para habilitar o RBS no SharePoint. O adaptador StorSimple para SharePoint funciona com RBS, permitindo que você mova os BLOBs para um servidor de backup pelo sistema de Microsoft Azure StorSimple. Microsoft Azure StorSimple, em seguida, armazena os dados de BLOB localmente ou na nuvem, com base no uso. Os BLOBs que são muito ativos (normalmente chamados de camada 1 ou dados ativos) residem localmente. Os dados menos ativos e os dados de arquivamento residem na nuvem. Depois de habilitar o RBS em um banco de dados de conteúdo, qualquer novo conteúdo de BLOB criado no SharePoint é armazenado no dispositivo StorSimple e não no banco de dados de conteúdo.

A implementação Microsoft Azure StorSimple do RBS oferece os seguintes benefícios:

* Ao mover o conteúdo do BLOB para um servidor separado, você pode reduzir a carga da consulta em SQL Server, o que pode melhorar SQL Server capacidade de resposta. 
* O Azure StorSimple usa eliminação de duplicação e compactação para reduzir o tamanho dos dados.
* O Azure StorSimple fornece proteção de dados na forma de instantâneos locais e na nuvem. Além disso, se você posicionar o banco de dados em si no dispositivo StorSimple, poderá fazer backup do banco de dados de conteúdo e dos BLOBs juntos de uma maneira consistente de falha. (Mover o banco de dados de conteúdo para o dispositivo só tem suporte para o dispositivo StorSimple 8000 Series. Este recurso não tem suporte para a série 5000 ou 7000.)
* O Azure StorSimple inclui recursos de recuperação de desastres, incluindo failover, recuperação de arquivos e volumes (incluindo recuperação de teste) e restauração rápida de dados.
* Você pode usar o software de recuperação de dados, como o Kroll Ontrack PowerControls, com instantâneos do StorSimple de dados de BLOB para executar a recuperação em nível de item do conteúdo do SharePoint. (Esse software de recuperação de dados é uma compra separada.)
* O adaptador StorSimple para SharePoint conecta-se ao portal de administração central do SharePoint, permitindo que você gerencie toda a sua solução do SharePoint a partir de um local central.

Mover o conteúdo do BLOB para o sistema de arquivos pode fornecer outros benefícios e economias de custo. Por exemplo, o uso do RBS pode reduzir a necessidade de um armazenamento de camada 1 caro e, como ele reduz o banco de dados de conteúdo, o RBS pode reduzir o número de bancos necessários no farm de servidores do SharePoint. No entanto, outros fatores, como limites de tamanho de banco de dados e a quantidade de conteúdo não RBS, também podem afetar os requisitos de armazenamento. Para obter mais informações sobre os custos e os benefícios de usar o RBS, consulte [Plan for RBS (SharePoint Foundation 2010)][4] e [decidindo usar o rbs no SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Limites de capacidade e desempenho
Antes de considerar o uso do RBS em sua solução do SharePoint, você deve estar ciente dos limites de desempenho e capacidade testados do SharePoint Server 2010 e do SharePoint Server 2013 e de como esses limites se relacionam com o desempenho aceitável. Para obter mais informações, consulte limites [de software e limites para o SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Examine o seguinte antes de configurar o RBS:

* Certifique-se de que o tamanho total do conteúdo (o tamanho de um banco de dados de conteúdo mais o tamanho de qualquer BLOB externo associado) não exceda o limite de tamanho RBS com suporte do SharePoint. Esse limite é de 200 GB. 
  
    **Para medir o banco de dados de conteúdo e o tamanho do BLOB**
  
  1. Execute esta consulta no WFE de administração central. Inicie o Shell de gerenciamento do SharePoint e, em seguida, insira o seguinte comando do Windows PowerShell para obter o tamanho dos bancos de dados de conteúdo:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Esta etapa Obtém o tamanho do banco de dados de conteúdo no disco.
  2. Execute uma das seguintes consultas SQL no SQL Management Studio na caixa SQL Server em cada banco de dados de conteúdo e adicione o resultado ao número obtido na etapa 1.
     
     Em bancos de dados de conteúdo do SharePoint 2013, digite:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     Em bancos de dados de conteúdo do SharePoint 2010, digite:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Esta etapa Obtém o tamanho dos BLOBs que foram externos.
* Recomendamos que você armazene todo o conteúdo de BLOB e banco de dados localmente no dispositivo StorSimple. O dispositivo StorSimple é um cluster de dois nós para alta disponibilidade. Colocar os bancos de dados de conteúdo e BLOBs no dispositivo StorSimple fornece alta disponibilidade.
  
    Use as práticas recomendadas de migração SQL Server tradicional para mover o banco de dados de conteúdo para o dispositivo StorSimple. Mova o banco de dados somente depois que todo o conteúdo do BLOB do banco de dados tiver sido movido para o compartilhamento de arquivos via RBS. Se você optar por mover o banco de dados de conteúdo para o dispositivo StorSimple, recomendamos que você configure o armazenamento do banco de dados de conteúdo no dispositivo como um volume primário.
* Em Microsoft Azure StorSimple, se você estiver usando volumes em camadas, não haverá nenhuma maneira de garantir que o conteúdo armazenado localmente no dispositivo StorSimple não será colocado em camadas para Microsoft Azure armazenamento em nuvem. Portanto, é recomendável usar volumes localmente afixados do StorSimple em conjunto com o RBS do SharePoint. Isso garantirá que todo o conteúdo do BLOB permaneça localmente no dispositivo StorSimple e não seja movido para Microsoft Azure.
* Se você não armazenar os bancos de dados de conteúdo no dispositivo StorSimple, use as práticas recomendadas tradicionais de SQL Server de alta disponibilidade que dão suporte ao RBS. O clustering de SQL Server dá suporte ao RBS, enquanto o espelhamento de SQL Server não. 

> [!WARNING]
> Se você não tiver habilitado o RBS, não recomendamos mover o banco de dados de conteúdo para o dispositivo StorSimple. Esta é uma configuração não testada.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>Instalação do adaptador StorSimple para SharePoint
Antes de instalar o adaptador StorSimple para SharePoint, você deve configurar o dispositivo StorSimple e certificar-se de que o farm de servidores do SharePoint e SQL Server instanciação atendam a todos os pré-requisitos. Este tutorial descreve os requisitos de configuração, bem como os procedimentos para instalar e atualizar o adaptador StorSimple para SharePoint.

## <a name="configure-prerequisites"></a>Configurar pré-requisitos
Antes de instalar o adaptador StorSimple para SharePoint, certifique-se de que o dispositivo StorSimple, farm de servidores do SharePoint e SQL Server instanciação atendam aos seguintes pré-requisitos.

### <a name="system-requirements"></a>Requisitos de sistema
O adaptador StorSimple para SharePoint funciona com o seguinte hardware e software:

* Sistema operacional com suporte – Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2
* Versões do SharePoint com suporte – SharePoint Server 2010 ou SharePoint Server 2013
* Versões SQL Server com suporte – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition ou SQL Server 2012 Enterprise Edition
* Dispositivos StorSimple com suporte – série StorSimple 8000, série StorSimple 7000 ou série StorSimple 5000.

### <a name="storsimple-device-configuration-prerequisites"></a>Pré-requisitos de configuração do dispositivo StorSimple
O dispositivo StorSimple é um dispositivo de bloco e, como tal, requer um servidor de arquivos no qual os dados possam ser hospedados. Recomendamos que você use um servidor separado em vez de um servidor existente do farm do SharePoint. Esse servidor de arquivos deve estar na mesma LAN (rede local) que o computador SQL Server que hospeda os bancos de dados de conteúdo.

> [!TIP]
> * Se você configurar seu farm do SharePoint para alta disponibilidade, também deverá implantar o servidor de arquivos para alta disponibilidade.
> * Se você não armazenar o banco de dados de conteúdo no dispositivo StorSimple, use as práticas recomendadas tradicionais de alta disponibilidade que dão suporte ao RBS. O clustering de SQL Server dá suporte ao RBS, enquanto o espelhamento de SQL Server não. 


Verifique se o dispositivo StorSimple está configurado corretamente e se os volumes apropriados para dar suporte à sua implantação do SharePoint estão configurados e acessíveis em seu computador SQL Server. Vá para [implantar seu dispositivo storsimple local](storsimple-8000-deployment-walkthrough-u2.md) se você ainda não tiver implantado e configurado seu dispositivo storsimple. Anote o endereço IP do dispositivo StorSimple; Você precisará dela durante a instalação do adaptador StorSimple para SharePoint.

Além disso, certifique-se de que o volume a ser usado para externalização de BLOB atende aos seguintes requisitos:

* O volume deve ser formatado com um tamanho de unidade de alocação de 64 KB.
* O WFE (front-end da Web) e os servidores de aplicativos devem ser capazes de acessar o volume por meio de um caminho UNC (Convenção de nomenclatura universal).
* O farm de servidores do SharePoint deve ser configurado para gravar no volume.

> [!NOTE]
> Depois de instalar e configurar o adaptador, toda a externalização de BLOB deve passar pelo dispositivo StorSimple (o dispositivo apresentará os volumes para SQL Server e gerenciar as camadas de armazenamento). Você não pode usar nenhum outro destino para externalização de BLOB.


Se você planeja usar o StorSimple Snapshot Manager para tirar instantâneos dos dados de BLOB e banco de dados, certifique-se de instalar o StorSimple Snapshot Manager no servidor de banco de dado para que ele possa usar o Serviço Gravador do SQL para implementar o Windows Serviço de Cópias de Sombra de Volume (VSS).

> [!IMPORTANT]
> O StorSimple Snapshot Manager não dá suporte ao gravador VSS do SharePoint e não pode obter instantâneos consistentes com o aplicativo de dados do SharePoint. Em um cenário do SharePoint, o StorSimple Snapshot Manager fornece apenas backups consistentes com falhas.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Pré-requisitos de configuração do farm do SharePoint
Verifique se o farm de servidores do SharePoint está configurado corretamente, da seguinte maneira:

* Verifique se o farm de servidores do SharePoint está em um estado íntegro e verifique o seguinte:
* Todos os servidores de aplicativos e WFE do SharePoint registrados no farm estão em execução e podem ser executados em ping do servidor no qual você instalará o adaptador StorSimple para SharePoint.
* O serviço de timer do SharePoint (SPTimerV3 ou SPTimerV4) está em execução em cada servidor WFE e servidor de aplicativos.
* O serviço de timer do SharePoint e o pool de aplicativos do IIS sob os quais o site de administração central do SharePoint está em execução têm privilégios administrativos.
* Verifique se o contexto de segurança aprimorado do Internet Explorer (IE ESC) está desabilitado. Siga estas etapas para desabilitar o IE ESC:
  
  1. Feche todas as instâncias do Internet Explorer.
  2. Inicie o Gerenciador do Servidor.
  3. No painel esquerdo, clique em **servidor local**.
  4. No painel direito, ao lado de **configuração de segurança reforçada do IE**, clique **em ativar**.
  5. Em **Administradores**, clique em **desativado**.
  6. Clique em **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Pré-requisitos do Remote BLOB Storage (RBS)
Verifique se você está usando uma versão com suporte do SQL Server. Somente as seguintes versões têm suporte e podem usar o RBS:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Os BLOBs podem ser externos somente nos volumes que o dispositivo StorSimple apresenta para SQL Server. Não há suporte para nenhum outro destino para externalização de BLOB.

Quando você tiver concluído todas as etapas de configuração de pré-requisito, vá para [instalar o adaptador StorSimple para SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instalar o adaptador StorSimple para SharePoint
Use as etapas a seguir para instalar o adaptador StorSimple para SharePoint. Se você estiver reinstalando o software, consulte [atualizar ou reinstalar o adaptador StorSimple para SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). O tempo necessário para a instalação depende do número total de bancos de dados do SharePoint em seu farm de servidores do SharePoint.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Configurar RBS
Depois de instalar o adaptador StorSimple para SharePoint, configure o RBS conforme descrito no procedimento a seguir.

> [!TIP]
> O adaptador StorSimple para SharePoint conecta-se à página de administração central do SharePoint, permitindo que o RBS seja habilitado ou desabilitado em cada banco de dados de conteúdo no farm do SharePoint. No entanto, habilitar ou desabilitar o RBS no banco de dados de conteúdo causa uma redefinição do IIS, que, dependendo da configuração do farm, pode interromper momentaneamente a disponibilidade do front-end da Web do SharePoint (WFE). (Fatores como o uso de um balanceador de carga de front-end, a carga de trabalho do servidor atual e assim por diante podem limitar ou eliminar essa interrupção). Para proteger os usuários de uma interrupção, recomendamos que você habilite ou desabilite o RBS somente durante uma janela de manutenção planejada.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Configurar a coleta de lixo
Quando os objetos são excluídos de um site do SharePoint, eles não são automaticamente excluídos do volume da loja RBS. Em vez disso, um programa de manutenção assíncrona, em segundo plano, exclui BLOBs órfãos do repositório de arquivos. Os administradores do sistema podem agendar esse processo para ser executado periodicamente ou podem iniciá-lo sempre que necessário.

Esse programa de manutenção (Microsoft. Data. SqlRemoteBlobs. Mantenedorer. exe) é instalado automaticamente em todos os servidores e servidores de aplicativos do SharePoint WFE quando você habilita o RBS. O programa é instalado no seguinte local: *unidade de inicialização*: \Program Files\Microsoft SQL Remote BLOB Storage 10.50 \ mantenedor \

Para obter informações sobre como configurar e usar o programa de manutenção, consulte [manter o RBS no SharePoint Server 2013][8].

> [!IMPORTANT]
> O programa mantenedor do RBS tem uso intensivo de recursos. Você deve agendá-lo para ser executado somente durante períodos de atividade leve no farm do SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Excluir BLOBs órfãos imediatamente
Se precisar excluir BLOBs órfãos imediatamente, você poderá usar as instruções a seguir. Observe que essas instruções são um exemplo de como isso pode ser feito em um ambiente do SharePoint 2013 com os seguintes componentes:

* O nome do banco de dados de conteúdo é WSS_Content.
* O nome do SQL Server é SHRPT13-SQL12\SHRPT13.
* O nome do aplicativo Web é SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Atualizar ou reinstalar o adaptador StorSimple para SharePoint
Use o procedimento a seguir para atualizar o SharePoint Server e reinstalar o adaptador StorSimple para SharePoint ou simplesmente atualizar ou reinstalar o adaptador em um farm de servidores do SharePoint existente.

> [!IMPORTANT]
> Examine as seguintes informações antes de tentar atualizar o software do SharePoint e/ou atualizar ou reinstalar o adaptador StorSimple para SharePoint:
> 
> * Todos os arquivos que foram movidos anteriormente para o armazenamento externo via RBS não estarão disponíveis até que a reinstalação seja concluída e o recurso RBS seja habilitado novamente. Para limitar o impacto do usuário, execute qualquer atualização ou reinstalação durante uma janela de manutenção planejada.
> * O tempo necessário para a atualização/reinstalação pode variar, dependendo do número total de bancos de dados do SharePoint no farm de servidores do SharePoint.
> * Após a conclusão da atualização/reinstalação, você precisará habilitar o RBS para os bancos de dados de conteúdo. Consulte [Configurar RBS](#configure-rbs) para obter mais informações.
> * Se você estiver configurando o RBS para um farm do SharePoint que tem um número muito grande de bancos de dados (maior que 200), a página **Administração Central do SharePoint** poderá atingir o tempo limite. Se isso ocorrer, atualize a página. Isso não afeta o processo de configuração.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>Remoção do adaptador StorSimple para SharePoint
Os procedimentos a seguir descrevem como mover os BLOBs de volta para os bancos de dados de conteúdo SQL Server e, em seguida, desinstalar o adaptador StorSimple para SharePoint. 

> [!IMPORTANT]
> Você precisa mover os BLOBs de volta para os bancos de dados de conteúdo antes de desinstalar o software do adaptador.


### <a name="before-you-begin"></a>Antes de começar
Colete as informações a seguir antes de mover os dados de volta para os bancos de SQL Server de conteúdo e iniciar o processo de remoção do adaptador:

* Os nomes de todos os bancos de dados para os quais o RBS está habilitado
* O caminho UNC do repositório de BLOB configurado

### <a name="move-the-blobs-back-to-the-content-databases"></a>Mover os BLOBs de volta para os bancos de dados de conteúdo
Antes de desinstalar o adaptador StorSimple para o software do SharePoint, você deve migrar todos os BLOBs que foram externos de volta para os bancos de dados de conteúdo de SQL Server. Se você tentar desinstalar o adaptador StorSimple para SharePoint antes de mover todos os BLOBs de volta para os bancos de dados de conteúdo, você verá a seguinte mensagem de aviso.

![Mensagem de aviso](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Para mover os BLOBs de volta para os bancos de dados de conteúdo
1. Baixe cada um dos objetos externos.
2. Abra a página **Administração Central do SharePoint** e navegue até **configurações do sistema**.
3. Em **Azure StorSimple**, clique em **Configurar adaptador StorSimple**.
4. Na página **Configurar adaptador StorSimple** , clique no botão **desabilitar** abaixo de cada um dos bancos de dados de conteúdo que você deseja remover do armazenamento de BLOBs externo. 
5. Exclua os objetos do SharePoint e carregue-os novamente.

Como alternativa, você pode usar o cmdlet do Microsoft `RBS Migrate()` PowerShell incluído com o SharePoint. Para obter mais informações, consulte [migrar conteúdo para dentro ou fora do RBS](https://technet.microsoft.com/library/ff628255.aspx).

Depois de mover os BLOBs de volta para o banco de dados de conteúdo, vá para a próxima etapa: [desinstalar o adaptador](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Desinstalar o adaptador
Depois de mover os BLOBs de volta para os bancos de dados de conteúdo SQL Server, use uma das opções a seguir para desinstalar o adaptador StorSimple para SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Para usar o programa de instalação para desinstalar o adaptador
1. Use uma conta com privilégios de administrador para fazer logon no servidor de front-end da Web (WFE).
2. Clique duas vezes no instalador do adaptador StorSimple para SharePoint. O assistente de instalação é iniciado.
   
    ![Assistente de instalação](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Clique em **Seguinte**. A página a seguir é exibida.
   
    ![Página de remoção do assistente de instalação](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Clique em **remover** para selecionar o processo de remoção. A página a seguir é exibida.
   
    ![Página de confirmação do assistente de instalação](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Clique em **remover** para confirmar a remoção. A página de progresso a seguir é exibida.
   
    ![Página de progresso do assistente de instalação](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Quando a remoção for concluída, a página concluir será exibida. Clique em **concluir** para fechar o assistente de instalação.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Para usar o painel de controle para desinstalar o adaptador
1. Abra o painel de controle e clique em **programas e recursos**.
2. Selecione **adaptador StorSimple para SharePoint**e clique em **desinstalar**.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
