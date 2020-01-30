---
title: Conector do SAP LaMa para Azure | Microsoft Docs
description: Gerenciando sistemas SAP no Azure usando o SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: 2df0bfe8041216e207193832c8f7ca48967c4e5b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842440"
---
# <a name="sap-lama-connector-for-azure"></a>Conector de SAP LaMa para o Azure

[1877727]: https://launchpad.support.sap.com/#/notes/1877727
[2343511]: https://launchpad.support.sap.com/#/notes/2343511
[2350235]: https://launchpad.support.sap.com/#/notes/2350235
[2562184]: https://launchpad.support.sap.com/#/notes/2562184
[2628497]: https://launchpad.support.sap.com/#/notes/2628497
[2445033]: https://launchpad.support.sap.com/#/notes/2445033
[2815988]: https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Instrução de suporte geral: sempre abra um incidente com o SAP no componente BC-VCM-LVM-HYPERV se você precisar de suporte para SAP LaMa ou o conector do Azure.

O SAP LaMa é usado por muitos clientes para operar e monitorar sua estrutura SAP. Desde o SAP LaMa 3,0 SP05, ele é fornecido com um conector para o Azure por padrão. Você pode usar esse conector para desalocar e iniciar máquinas virtuais, copiar e realocar discos gerenciados e excluir discos gerenciados. Com essas operações básicas, você pode realocar, copiar, clonar e atualizar sistemas SAP usando o SAP LaMa.

Este guia descreve como você configura o conector do Azure para SAP LaMa, cria máquinas virtuais que podem ser usadas para instalar sistemas SAP adaptáveis e como configurá-los.

> [!NOTE]
> O conector está disponível apenas no SAP LaMa Enterprise Edition

## <a name="resources"></a>Recursos

As seguintes notas SAP estão relacionadas ao tópico do SAP LaMa no Azure:

| Número da nota | Título |
| --- | --- |
| [2343511] |Conector de Microsoft Azure para SAP Landscape Management (LaMa) |
| [2350235] |SAP Landscape Management 3,0-Enterprise Edition |

Leia também o [SAP Help portal for SAP lama](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Comentários gerais

* Certifique-se de habilitar a *criação automática de mountpoint* nas configurações de > de instalação – mecanismo de >  
  Se o SAP LaMa montar volumes usando as extensões adaptáveis do SAP em uma máquina virtual, o ponto de montagem deverá existir se essa configuração não estiver habilitada.

* Use uma sub-rede separada e não use endereços IP dinâmicos para impedir que o endereço IP "roubando" ao implantar novas VMs e instâncias SAP sejam despreparadas  
  Se você usar a alocação de endereço IP dinâmico na sub-rede, que também é usada pelo SAP LaMa, a preparação de um sistema SAP com o SAP LaMa poderá falhar. Se um sistema SAP estiver despreparado, os endereços IP não serão reservados e poderão ser alocados para outras máquinas virtuais.

* Se você entrar em hosts gerenciados, certifique-se de não bloquear os sistemas de arquivos de serem desmontados  
  Se você entrar em máquinas virtuais do Linux e alterar o diretório de trabalho para um diretório em um ponto de montagem, por exemplo,/usr/sap/AH1/ASCS00/exe, o volume não poderá ser desmontado e uma falha de reutilização ou despreparação.

* Certifique-se de que desativa as CLOUD_NETCONFIG_MANAGE nas máquinas virtuais SUSE SLES Linux. Para obter mais detalhes, consulte [SuSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633).

## <a name="set-up-azure-connector-for-sap-lama"></a>Configurar o conector do Azure para SAP LaMa

O conector do Azure é fornecido a partir do SAP LaMa 3,0 SP05. Recomendamos sempre instalar o pacote de suporte mais recente e o patch para o SAP LaMa 3,0.

O conector do Azure usa a API Azure Resource Manager para gerenciar os recursos do Azure. O SAP LaMa pode usar uma entidade de serviço ou uma identidade gerenciada para se autenticar nessa API. Se o LaMa do SAP estiver em execução em uma VM do Azure, é recomendável usar uma identidade gerenciada, conforme descrito no capítulo [usar uma identidade gerenciada para obter acesso à API do Azure](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d). Se você quiser usar uma entidade de serviço, siga as etapas no capítulo [usar uma entidade de serviço para obter acesso à API do Azure](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e).

### <a name="913c222a-3754-487f-9c89-983c82da641e"></a>Usar uma entidade de serviço para obter acesso à API do Azure

O conector do Azure pode usar uma entidade de serviço para autorizar contra Microsoft Azure. Siga estas etapas para criar uma entidade de serviço para o SAP Landscape Management (LaMa).

1. Ir para https://portal.azure.com
1. Abra o painel Azure Active Directory
1. Clique em Registros de aplicativo
1. Clique em novo registro
1. Insira um nome e clique em registrar
1. Selecione o novo aplicativo e clique em certificados & segredos na guia Configurações
1. Crie um novo segredo de cliente, insira uma descrição para uma nova chave, selecione quando o segredo deve expirar e clique em Guardar
1. Anote o valor. Ele é usado como a senha para a entidade de serviço
1. Anote o ID da aplicação. Ele é usado como o nome de usuário da entidade de serviço

O Principal de serviço não tem permissões para aceder aos seus recursos do Azure por predefinição. Você precisa conceder permissões de entidade de serviço para acessá-las.

1. Ir para https://portal.azure.com
1. Abra a folha grupos de recursos
1. Selecione o grupo de recursos que você deseja usar
1. Clique em controle de acesso (IAM)
1. Clique em Adicionar atribuição de função
1. Selecione o colaborador da função
1. Introduza o nome da aplicação que criou acima
1. Clique em Guardar.
1. Repita a etapa de 3 a 8 para todos os grupos de recursos que você deseja usar no SAP LaMa

### <a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Usar uma identidade gerenciada para obter acesso à API do Azure

Para poder usar uma identidade gerenciada, sua instância do SAP LaMa deve ser executada em uma VM do Azure que tenha uma identidade de sistema ou de usuário atribuída. Para obter mais informações sobre identidades gerenciadas, leia [o que são identidades gerenciadas para recursos do Azure?](../../../active-directory/managed-identities-azure-resources/overview.md) e [Configure as identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md).

A identidade gerenciada não tem permissões para acessar os recursos do Azure por padrão. Você precisa conceder a ele permissões para acessá-los.

1. Ir para https://portal.azure.com
1. Abra a folha grupos de recursos
1. Selecione o grupo de recursos que você deseja usar
1. Clique em controle de acesso (IAM)
1. Clique em Adicionar > Adicionar atribuição de função
1. Selecione o colaborador da função
1. Selecione ' máquina virtual ' para ' atribuir acesso a '
1. Selecione a máquina virtual na qual sua instância do SAP LaMa está sendo executada
1. Clique em Guardar.
1. Repita as etapas para todos os grupos de recursos que você deseja usar no SAP LaMa

Na configuração do conector do Azure para SAP LaMa, selecione ' usar identidade gerenciada ' para habilitar o uso da identidade gerenciada. Se você quiser usar uma identidade atribuída pelo sistema, deixe o campo nome de usuário vazio. Se você quiser usar uma identidade atribuída pelo usuário, insira a ID de identidade atribuída pelo usuário no campo nome de usuário.

### <a name="create-a-new-connector-in-sap-lama"></a>Criar um novo conector no SAP LaMa

Abra o site do SAP LaMa e navegue até infraestrutura. Acesse a guia gerenciadores de nuvem e clique em Adicionar. Selecione o Adaptador de Nuvem de Microsoft Azure e clique em Avançar. Introduza as seguintes informações:

* Rótulo: escolha um nome para a instância do conector
* Nome de usuário: ID do aplicativo da entidade de serviço ou ID da identidade atribuída ao usuário da máquina virtual. Consulte [Utilização de um Sistema ou Identidade Atribuída ao Utilizador] para obter mais informações
* Senha: chave/senha da entidade de serviço. Você pode deixar esse campo vazio se usar um sistema ou uma identidade atribuída pelo usuário.
* URL: manter https://management.azure.com/ padrão
* Intervalo de monitoramento (segundos): deve ser pelo menos 300
* Usar identidade gerenciada: o SAP LaMa pode usar um sistema ou uma identidade atribuída pelo usuário para se autenticar na API do Azure. Consulte [o capítulo usar uma identidade gerenciada para obter acesso à API do Azure](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) neste guia.
* ID da assinatura: ID da assinatura do Azure
* ID de locatário Azure Active Directory: ID do locatário Active Directory
* Host de proxy: nome de host do proxy se o SAP LaMa precisar de um proxy para se conectar à Internet
* Porta do proxy: porta TCP do proxy
* Alterar tipo de armazenamento para economizar custos: habilite essa configuração se o adaptador do Azure precisar alterar o tipo de armazenamento do Managed Disks para economizar custos quando os discos não estiverem em uso. Para discos de dados que são referenciados em uma configuração de instância do SAP, o adaptador alterará o tipo de disco para o armazenamento padrão durante uma instância despreparar e de volta para o tipo de armazenamento original durante uma preparação de instância. Se você parar uma máquina virtual no SAP LaMa, o adaptador alterará o tipo de armazenamento de todos os discos anexados, incluindo o disco do sistema operacional para o armazenamento padrão. Se você iniciar uma máquina virtual no SAP LaMa, o adaptador irá alterar o tipo de armazenamento de volta para o tipo de armazenamento original.

Clique em configuração de teste para validar sua entrada. Você deve ver

Conexão bem-sucedida: a conexão com o Microsoft Cloud foi bem-sucedida. 7 grupos de recursos encontrados (somente 10 grupos solicitados)

na parte inferior do site.

## <a name="provision-a-new-adaptive-sap-system"></a>Provisionar um novo sistema SAP adaptável

Você pode implantar manualmente uma nova máquina virtual ou usar um dos modelos do Azure no [repositório de início rápido](https://github.com/Azure/azure-quickstart-templates). Ele contém modelos para [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [servidores de aplicativos SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)e o [banco de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Você também pode usar esses modelos para provisionar novos hosts como parte de uma cópia/clonagem do sistema, etc.

É recomendável usar uma sub-rede separada para todas as máquinas virtuais que você deseja gerenciar com o SAP LaMa e não usar endereços IP dinâmicos para impedir que o endereço IP "roubando" ao implantar novas máquinas virtuais e instâncias SAP sejam despreparadas.

> [!NOTE]
> Se possível, remova todas as extensões de máquina virtual, pois elas podem causar tempos de execução longos para desanexar discos de uma máquina virtual.

Verifique se o usuário \<hanasid > ADM, \<sapsid > ADM e os SAPs de grupo existem no computador de destino com a mesma ID e GID ou use LDAP. Habilite e inicie o servidor NFS nas máquinas virtuais que devem ser usadas para executar o SAP NetWeaver (A) SCS.

### <a name="manual-deployment"></a>Implantação Manual

O SAP LaMa se comunica com a máquina virtual usando o agente de host do SAP. Se você implantar as máquinas virtuais manualmente ou não usar o modelo de Azure Resource Manager do repositório de início rápido, certifique-se de instalar o agente de host do SAP e as extensões adaptáveis do SAP mais recentes. Para obter mais informações sobre os níveis de patch necessários para o Azure, consulte a observação do SAP [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Implantação manual de uma máquina virtual Linux

Crie uma nova máquina virtual com um dos sistemas operacionais com suporte listados na observação SAP [2343511]. Adicione outras configurações de IP para as instâncias do SAP. Cada instância precisa de pelo menos no endereço IP e deve ser instalada usando um nome de host virtual.

A instância ASCS do SAP NetWeaver precisa de discos para/sapmnt/\<SAPSid >,/usr/SAP/\<SAPSid >,/usr/SAP/trans e/usr/SAP/\<sapsid > ADM. Os servidores de aplicativos SAP NetWeaver não precisam de discos adicionais. Tudo relacionado à instância do SAP deve ser armazenado no ASCS e exportado via NFS. Caso contrário, no momento, não é possível adicionar servidores de aplicativos adicionais usando o SAP LaMa.

![SAP NetWeaver ASCS no Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Implantação manual para SAP HANA

Crie uma nova máquina virtual com um dos sistemas operacionais com suporte para SAP HANA, conforme listado em SAP Note [2343511]. Adicione uma configuração de IP adicional para SAP HANA e outra por locatário do HANA.

SAP HANA precisa de discos para/Hana/Shared,/Hana/backup,/Hana/data e/Hana/log

![SAP HANA no Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Implantação manual para Oracle Database no Linux

Crie uma nova máquina virtual com um dos sistemas operacionais com suporte para bancos de dados Oracle, conforme listado em SAP Note [2343511]. Adicione uma configuração de IP adicional para o banco de dados Oracle.

O banco de dados Oracle precisa de discos para/Oracle,/Home/oraod1 e/Home/Oracle

![Banco de dados Oracle no Linux](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Implantação manual para Microsoft SQL Server

Crie uma nova máquina virtual com um dos sistemas operacionais com suporte para Microsoft SQL Server, conforme listado em SAP Note [2343511]. Adicione uma configuração de IP adicional para a instância de SQL Server.

O servidor de banco de dados de SQL Server precisa de discos para os arquivos de log e os discos de banco de c:\usr\sap.

![Banco de dados Oracle no Linux](media/lama/sap-lama-db-sql.png)

Certifique-se de instalar um driver ODBC da Microsoft com suporte para SQL Server em uma máquina virtual que você deseja usar para realocar um servidor de aplicativos do SAP NetWeaver para ou como um destino de cópia/clonagem do sistema.

O SAP LaMa não pode realocar SQL Server em si, de modo que uma máquina virtual que você deseja usar para realocar uma instância de banco de dados para ou como um destino de cópia/clonagem do sistema precisa SQL Server pré-instalado.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Implantar a máquina virtual usando um modelo do Azure

Baixe os seguintes arquivos disponíveis mais recentes do [Marketplace de software SAP](https://support.sap.com/swdc) para o sistema operacional das máquinas virtuais:

1. SAPCAR 7,21
1. AGENTE DE HOST DO SAP 7,21
1. EXTENSÃO ADAPTÁVEL DO SAP 1,0 EXT

Baixe também os seguintes componentes do [centro de download da Microsoft](https://www.microsoft.com/download)

1. Pacote redistribuível do Microsoft Visual C++ 2010 (x64) (somente Windows)
1. Microsoft ODBC Driver for SQL Server (somente SQL Server)

Os componentes são necessários para implantar o modelo. A maneira mais fácil de torná-los disponíveis para o modelo é carregá-los em uma conta de armazenamento do Azure e criar uma SAS (assinatura de acesso compartilhado).

Os modelos têm os seguintes parâmetros:

* sapSystemId: a ID do sistema SAP. Ele é usado para criar o layout do disco (por exemplo,/usr/SAP/\<sapsid >).

* ComputerName: o nome do computador da nova máquina virtual. Esse parâmetro também é usado pelo SAP LaMa. Quando você usa esse modelo para provisionar uma nova máquina virtual como parte de uma cópia do sistema, o SAP LaMa aguarda até que o host com esse nome de computador possa ser acessado.

* osType: o tipo do sistema operacional que você deseja implantar.

* dbType: o tipo do banco de dados. Esse parâmetro é usado para determinar quantas configurações de IP adicionais precisam ser adicionadas e como deve ser a aparência do layout do disco.

* sapSystemSize: o tamanho do sistema SAP que você deseja implantar. Ele é usado para determinar o tamanho e o tipo da instância de máquina virtual.

* adminUsername: nome de usuário para a máquina virtual.

* adminPassword: senha para a máquina virtual. Você também pode fornecer uma chave pública para o SSH.

* sshKeyData: chave SSH pública para as máquinas virtuais. Com suporte apenas para sistemas operacionais Linux.

* subnetid: a ID da sub-rede que você deseja usar.

* deployEmptyTarget: você pode implantar um destino vazio se quiser usar a máquina virtual como um destino para uma instância realocar ou semelhante. Nesse caso, nenhum disco adicional ou configuração de IP está anexado.

* sapcarLocation: o local do aplicativo SAPCAR que corresponde ao sistema operacional que você implanta. SAPCAR é usado para extrair os arquivos mortos que você fornece em outros parâmetros.

* sapHostAgentArchiveLocation: o local do arquivo morto do agente de host do SAP. O agente de host SAP é implantado como parte desta implantação de modelo.

* sapacExtLocation: o local das extensões adaptáveis do SAP. A observação do SAP [2343511] lista o nível mínimo de patch necessário para o Azure.

* vcRedistLocation: o local do tempo de execução do VC que é necessário para instalar as extensões adaptáveis do SAP. Esse parâmetro só é necessário para o Windows.

* odbcDriverLocation: o local do driver ODBC que você deseja instalar. Há suporte apenas para o Microsoft ODBC driver for SQL Server.

* sapadmPassword: a senha para o usuário sapadm.

* sapadmId: a ID de usuário do Linux do usuário sapadm. Não é necessário para o Windows.

* sapsysGid: a ID do grupo do Linux do grupo de SAPS. Não é necessário para o Windows.

* _artifactsLocation: A base URI, onde estão localizados artefactos exigidos por este modelo. Quando o modelo é implantado usando os scripts que o acompanha, um local privado na assinatura será usado e esse valor será gerado automaticamente. Necessário apenas se você não implantar o modelo do GitHub.

* _artifactsLocationSasToken: O sasToken necessário para aceder _artifactsLocation. Quando o modelo for implantado usando os scripts que o acompanha, um sasToken será gerado automaticamente. Necessário apenas se você não implantar o modelo do GitHub.

### <a name="sap-hana"></a>SAP HANA

Nos exemplos a seguir, presumimos que você instale SAP HANA com a ID do sistema HN1 e o sistema SAP NetWeaver com a ID do sistema AH1. Os nomes de host virtuais são hn1 para a instância do HANA, ah1-DB para o locatário do HANA usado pelo sistema SAP NetWeaver, ah1-ASCs para o SAP NetWeaver ASCS e ah1-di-0 para o primeiro servidor de aplicativos SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Instalar o ASCS do SAP NetWeaver para SAP HANA usando o Azure Managed Disks

Antes de iniciar o SWPM (Gerenciador de provisionamento de software) SAP, você precisa montar o endereço IP do nome de host virtual do ASCS. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando o sapacext, certifique-se de remontar o endereço IP após uma reinicialização.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Execute SWPM e use *ah1-ASCs* para o *nome de host da instância ASCs*.

![Linux][Logo_Linux] Linux  
Adicione o seguinte parâmetro de perfil ao perfil do Agente hospedeiro SAP, que está localizado em /usr/sap/hostctrl/exe/host_profile. Para obter mais informações, consulte SAP Note [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Instalar o ASCS do SAP NetWeaver para SAP HANA no Azure NetAppFiles (seja) BETA

> [!NOTE]
> Essa funcionalidade ainda é nem GA. Para obter mais informações, consulte SAP Note [2815988] (visível somente para clientes de visualização).
Abra um incidente SAP no componente BC-VCM-LVM-HYPERV e solicite ingressar no adaptador de armazenamento LaMa para Azure NetApp Files visualização

O seja fornece NFS para Azure. No contexto do SAP LaMa, isso simplifica a criação de instâncias ASCS (ABAP central Services) e a instalação subsequente de servidores de aplicativos. Anteriormente, a instância ASCS também tinha de funcionar como servidor NFS e o parâmetro acosprep/nfs_paths teve de ser adicionado ao host_profile do Hostagent SAP.

#### <a name="anf-is-currently-available-in-these-regions"></a>O seja está disponível atualmente nestas regiões:

Leste da Austrália, EUA Central, leste dos EUA, leste dos EUA 2, Europa Setentrional, Sul EUA Central, Europa Ocidental e oeste dos EUA 2.

#### <a name="network-requirements"></a>Requisitos de rede

O seja requer uma sub-rede delegada que deve fazer parte da mesma VNET que os servidores SAP. Aqui está um exemplo para essa configuração.
Esta tela mostra a criação da VNET e a primeira sub-rede:

![SAP LaMa criar rede virtual para seja do Azure ](media/lama/sap-lama-createvn-50.png)

A próxima etapa cria a sub-rede delegada para Microsoft. NetApp/volumes.

![LaMa do SAP-adicionar sub-rede delegada ](media/lama/sap-lama-addsubnet-50.png)

![Lista de sub-redes do SAP LaMa ](media/lama/sap-lama-subnets.png)

Agora, uma conta do NetApp precisa ser criada dentro do portal do Azure:

![SAP LaMa criar conta do NetApp ](media/lama/sap-lama-create-netappaccount-50.png)

![Conta do SAP LaMa NetApp criada ](media/lama/sap-lama-netappaccount.png)

Na conta do NetApp, o pool de capacidade especifica o tamanho e o tipo de discos para cada pool:

![SAP LaMa criar pool de capacidade do NetApp ](media/lama/sap-lama-capacitypool-50.png)

![Pool de capacidade do SAP LaMa NetApp criado ](media/lama/sap-lama-capacitypool-list.png)

Os volumes de NFS agora podem ser definidos. Como haverá volumes para vários sistemas em um pool, um esquema de nomenclatura autoexplicado deverá ser escolhido. Adicionar o SID ajuda a agrupar volumes relacionados juntos. Para o ASCS e a instância as, as seguintes montagens são necessárias: */sapmnt/\<sid\>* , */usr/sap/\<Sid\>* e */Home/\<Sid\>ADM*. Opcionalmente, */usr/SAP/trans* é necessário para o diretório de transporte central, que é pelo menos usado por todos os sistemas de um cenário.

> [!NOTE]
> Durante a fase BETA, o nome dos volumes deve ser exclusivo na assinatura.

![SAP LaMa criar um volume 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa criar um volume 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa criar um volume 3 ](media/lama/sap-lama-createvolume3-80.png)

Essas etapas também precisam ser repetidas para os outros volumes.

![Lista de volumes criados do SAP LaMa ](media/lama/sap-lama-volumes.png)

Agora esses volumes precisam ser montados nos sistemas em que a instalação inicial com o SAP SWPM será executada.

Primeiro, os pontos de montagem precisam ser criados. Nesse caso, o SID é AN1 para que os seguintes comandos precisem ser executados:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Em seguida, os volumes seja serão montados com os seguintes comandos:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
Os comandos mount também podem ser derivados do Portal. Os pontos de montagem locais precisam ser ajustados.

Use o comando df-h para verificar.

![Nível de sistema operacional dos pontos de montagem do SAP LaMa ](media/lama/sap-lama-mounts.png)

Agora, a instalação com SWPM deve ser executada.

As mesmas etapas devem ser executadas para pelo menos uma instância do AS.

Após a instalação bem-sucedida, o sistema deve ser descoberto no SAP LaMa.

Os pontos de montagem devem ser assim para o ASCS e a instância do as:

![pontos de montagem SAP LaMa em LaMa ](media/lama/sap-lama-ascs.png) (Este é um exemplo. Os endereços IP e o caminho de exportação são diferentes dos usados antes)


#### <a name="install-sap-hana"></a>Instalar o SAP HANA

Se você instalar SAP HANA usando a ferramenta de linha de comando hdblcm, use o parâmetro--HostName para fornecer um nome de host virtual. Você precisa adicionar o endereço IP do nome de host virtual do banco de dados a uma interface de rede. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando o sapacext, certifique-se de remontar o endereço IP após uma reinicialização.

Adicione outro nome de host virtual e endereço IP para o Name que é usado pelos servidores de aplicativos para se conectar ao locatário do HANA.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Execute a instalação da instância do banco de dados do SWPM na máquina virtual do servidor de aplicativos, não na máquina virtual HANA. Use *ah1-DB* para o *host de banco de dados* no *banco de dados de diálogo para o sistema SAP*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Instalar o servidor de aplicativos SAP NetWeaver para SAP HANA

Antes de iniciar o SWPM (Gerenciador de provisionamento de software) SAP, você precisa montar o endereço IP do nome de host virtual do servidor de aplicativos. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando o sapacext, certifique-se de remontar o endereço IP após uma reinicialização.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Recomenda-se utilizar o parâmetro de perfil SAP NetWeaver dbs/hdb/hdb_use_ident para definir a identidade que é usada para encontrar a chave na userstore HDB. Você pode adicionar esse parâmetro manualmente após a instalação da instância do banco de dados com SWPM ou executar SWPM com

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Se você defini-lo manualmente, também precisará criar novas entradas de USERSTORE do HDB.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Use *ah1-di-0* para o *nome de host da instância do Pas* na *instância do servidor de aplicativos primário*de diálogo.

#### <a name="post-installation-steps-for-sap-hana"></a>Etapas pós-instalação para SAP HANA

Certifique-se de fazer backup do SYSTEMDB e de todos os bancos de dados de locatário antes de tentar fazer uma cópia de locatário, mover o locatário ou criar uma replicação de sistema.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

Nos exemplos a seguir, presumimos que você instale o sistema SAP NetWeaver com a ID do sistema AS1. Os nomes de host virtual são AS1 para a instância de SQL Server usada pelo sistema SAP NetWeaver, AS1-ASCs para o SAP NetWeaver ASCS e AS1-di-0 para o primeiro servidor de aplicativos SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Instalar o ASCS do SAP NetWeaver para SQL Server

Antes de iniciar o SWPM (Gerenciador de provisionamento de software) SAP, você precisa montar o endereço IP do nome de host virtual do ASCS. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando o sapacext, certifique-se de remontar o endereço IP após uma reinicialização.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Execute SWPM e use *AS1-ASCs* para o *nome de host da instância ASCs*.

#### <a name="install-sql-server"></a>Instalar o Servidor SQL

Você precisa adicionar o endereço IP do nome de host virtual do banco de dados a uma interface de rede. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando o sapacext, certifique-se de remontar o endereço IP após uma reinicialização.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Execute a instalação da instância do banco de dados do SWPM na máquina virtual do SQL Server. Utilize SAPINST_USE_HOSTNAME=*as1-db* para anular o nome de anfitrião usado para ligar ao SQL Server. Se você implantou a máquina virtual usando o modelo de Azure Resource Manager, certifique-se de definir o diretório usado para os arquivos de dados de banco de dados para *C:\sql\data* e o arquivo de log de banco de dados para *C:\sql\log*.

Certifique-se de que o usuário *NT AUTHORITY\SYSTEM* tenha acesso ao SQL Server e tenha a função de servidor *sysadmin*. Para obter mais informações, consulte a observação do SAP [1877727] e [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Instalar o servidor de aplicativos SAP NetWeaver

Antes de iniciar o SWPM (Gerenciador de provisionamento de software) SAP, você precisa montar o endereço IP do nome de host virtual do servidor de aplicativos. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando o sapacext, certifique-se de remontar o endereço IP após uma reinicialização.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Use *AS1-di-0* para o *nome de host da instância do Pas* na *instância do servidor de aplicativos primário*de diálogo.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="errors-and-warnings-during-discover"></a>Erros e avisos durante a descoberta

* A permissão SELECT foi negada
  * [Microsoft] [ODBC SQL Server Driver] [SQL Server] A permissão SELECT foi negada no objeto 'log_shipping_primary_databases', base de dados 'msdb', schema 'dbo'. [SOAPFaultException]  
  A permissão SELECT foi negada no objeto 'log_shipping_primary_databases', base de dados 'msdb', schema 'dbo'.
  * Solução  
    Verifique se o *NT AUTHORITY\SYSTEM* pode acessar o SQL Server. Consulte a observação do SAP [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Erros e avisos de validação de instância

* Uma exceção foi gerada na validação do USERSTORE do HDB  
  * consulte o Visualizador de log  
    com.sap.nw.lm.aci.monitor.api.validação.RuntimeValidação: Exceção em validador com ID 'RuntimeHDBConnectionValidator' (Validação: 'VALIDATION_HDB_USERSTORE'): Não conseguiu recuperar a hdbuserstore  
    O USERSTORE do HANA não está no local correto
  * Solução  
    Verifique se o/usr/sap/AH1/hdbclient/install/installation.ini está correto

### <a name="errors-and-warnings-during-a-system-copy"></a>Erros e avisos durante uma cópia do sistema

* Ocorreu um erro ao validar a etapa de provisionamento do sistema
  * Causada por: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN 1 -f 50 -h hn1-db -o nível=0\;status=5\;porta=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;porta=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Solução  
    Fazer backup de todos os bancos de dados no sistema HANA de origem

* *Início* da etapa de cópia do sistema da instância do banco de dados
  * Falha na operação ' 000D3A282BC91EE8A1D76CF1F92E2944 ' do agente de host (OperationException. FaultCode: ' 127 ', mensagem: ' falha na execução do comando. : [Microsoft][ODBC SQL Server Driver][SQL Server]O utilizador não tem permissão para alterar a base de dados 'AS2', a base de dados não existe, ou a base de dados não está num estado que permite verificações de acesso.')
  * Solução  
    Verifique se o *NT AUTHORITY\SYSTEM* pode acessar o SQL Server. Consulte a observação do SAP [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Erros e avisos durante um clone do sistema

* Erro ao tentar registrar o agente de instância na etapa *registro forçado e iniciar o agente de instância* do servidor de aplicativos ou ASCS
  * Erro ao tentar registrar o agente de instância. (RemoteException: 'Falha em carregar dados de instância sapmnt\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Não pode aceder ao perfil '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0": Não pode aceder ao perfil '  as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': No tal ficheiro ou diretório.')
  * Solução  
   Certifique-se de que a parte sapmnt no ASCS/SCS tem acesso total para SAP_AS1_GlobalAdmin

* Erro na etapa *habilitar proteção de inicialização para clone*
  * Falha em abrir o ficheiro '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' Causa: Não existe tal ficheiro ou diretório
  * Solução  
    A conta de computador do servidor de aplicativos precisa de acesso de gravação ao perfil

### <a name="errors-and-warnings-during-create-system-replication"></a>Erros e avisos durante a criação da replicação do sistema

* Exceção ao clicar em criar replicação do sistema
  * Causada por: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN 1 -f 50 -h hn1-db -o nível=0\;status=5\;porta=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;porta=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Solução  
    Testar se sapacext pode ser executado como `<hanasid`> ADM

* Erro quando a cópia completa não está habilitada na etapa de armazenamento
  * Ocorreu um erro ao relatar uma mensagem de atributo de contexto para o caminho IStorageCopyData. storageVolumeCopyList: 1 e o campo targetStorageSystemId
  * Solução  
    Ignore os avisos na etapa e tente novamente. Esse problema será corrigido em um novo pacote de suporte/patch do SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Erros e avisos durante a realocação

* O caminho '/usr/sap/AH1 ' não é permitido para reexportaçãos de NFS.
  * Consulte a observação do SAP [2628497] para obter detalhes.
  * Solução  
    Adicione exportações ASCS ao perfil ASCS HostAgent. Consulte a observação do SAP [2628497]

* Função não implementada ao realocar ASCS
  * Saída do comando: exportfs: host:/usr/SAP/AX1: função não implementada
  * Solução  
    Verifique se o serviço servidor NFS está habilitado na máquina virtual realocar destino

### <a name="errors-and-warnings-during-application-server-installation"></a>Erros e avisos durante a instalação do servidor de aplicativos

* Erro ao executar a etapa SAPinst: getProfileDir
  * ERRO: (Último erro relatado pelo passo: Caught ESAPinstException in module call: Validator of step ' NW_DI indind, indind, 0. NW_GetSidFromProfiles indind, indind, NW_readProfileDirindindindindindindindindindindreadProfile0getProfileDir' relatou um erro: O nó \\\as1-ascs\sapmnt\AS1\SYS\perfil não existe. Iniciar o SAPinst no modo interativo para resolver esse problema)
  * Solução  
    Verifique se o SWPM está em execução com um usuário que tem acesso ao perfil. Este usuário pode ser configurado no assistente de instalação do servidor de aplicativos

* Erro ao executar a etapa SAPinst: askUnicode
  * ERRO: (Último erro relatado pelo passo: Caught ESAPinstException in module call: Validator of step ' NW_DI indind, indind, 0. NW_GetSidFromProfiles indind, indind, NW_getUnicode indindindindindindindindind, indind, relatou um erro: Iniciar o SAPinst em modo interativo para resolver este problema)
  * Solução  
    Se você usar um kernel SAP recente, o SWPM não poderá determinar se o sistema é mais um sistema Unicode usando o servidor de mensagens do ASCS. Consulte a observação do SAP [2445033] para obter mais detalhes.  
    Esse problema será corrigido em um novo pacote de suporte/patch do SAP LaMa.  
    Defina o parâmetro de perfil OS_UNICODE=uc no perfil predefinido do seu sistema SAP para contornar este problema.

* Erro ao executar a etapa SAPinst: dCheckGivenServer
  * Erro ao executar a etapa SAPinst: dCheckGivenServer "Version =" 1.0 "erro: (último erro relatado pela etapa: \<p > instalação foi cancelada pelo usuário. \</p>
  * Solução  
    Verifique se o SWPM está em execução com um usuário que tem acesso ao perfil. Este usuário pode ser configurado no assistente de instalação do servidor de aplicativos

* Erro ao executar a etapa SAPinst: checkClient
  * Erro ao executar a etapa SAPinst: checkClient "Version =" 1.0 "erro: (último erro relatado pela etapa: \<p > instalação foi cancelada pelo usuário. \</p>)
  * Solução  
    Verifique se o Microsoft ODBC driver for SQL Server está instalado na máquina virtual na qual você deseja instalar o servidor de aplicativos

* Erro ao executar a etapa SAPinst: copyScripts
  * Último erro relatado pela etapa: falha na chamada do sistema. DETALHES: Erro 13 (0x00000000d) (Permissão negada) na execução da chamada do sistema 'fopenU' com parâmetro (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), linha (494) em ficheiro (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), traço de pilha:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  CSiServiceSet. cpp: 63: CSiServiceSet:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: Call ()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction (iastring const& name, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile. cpp: 233: CSyFileImpl:: openStream (ISyFile:: eFileOpenMode)  
  syxxcfstrm. cpp: 29: CSyFileStreamImpl:: CSyFileStreamImpl (CSyFileStream *, iastring, ISyFile:: eFileOpenMode)  
  syxxcfstrm. cpp: 265: CSyFileStreamImpl:: Open ()  
  syxxcfstrm2. cpp: 58: CSyFileStream2Impl:: CSyFileStream2Impl (const CSyPath & \\\ AW1-ASCs/sapmnt/AW1/SYS/exe/UC/NTAMD64/strdbs. cmd, 0x4)  
  syxxcfstrm2. cpp: 456: CSyFileStream2Impl:: Open ()
  * Solução  
    Verifique se o SWPM está em execução com um usuário que tem acesso ao perfil. Este usuário pode ser configurado no assistente de instalação do servidor de aplicativos

* Erro ao executar a etapa SAPinst: askPasswords
  * Último erro relatado pela etapa: falha na chamada do sistema. DETALHES: Erro 5 (0x000000005) (O acesso é negado.) na execução da chamada de sistema 'NetValidatePasswordPolicy' com parâmetro (...), linha (359) em ficheiro (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), traço de pilha:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  CSiServiceSet. cpp: 63: CSiServiceSet:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: Call ()  
  CSiStepExecute. cpp: 764: CSiStepExecute:: invokeDialog ()  
  DarkModeGuiEngine. cpp: 56: DarkModeGuiEngine:: showDialogCalledByJs ()  
  DarkModeDialog. cpp: 85: DarkModeDialog:: Submit ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: Call ()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction (iastring const& name, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validarPasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg. cpp: 297: ISyAccountMgt::P asswordValidationMessage CSyAccountMgtImpl:: validatePasswordPolicy (saponazure, * * * * *) const)
  * Solução  
    Certifique-se de adicionar uma regra de host em etapa de *isolamento* para permitir a comunicação da VM para o controlador de domínio

## <a name="next-steps"></a>Passos seguintes
* [Manual de operações do SAP HANA no Azure][hana-ops-guide]
* [Planejamento e implementação de máquinas virtuais do Azure para SAP][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
