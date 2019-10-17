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
ms.openlocfilehash: 6521c139463bb0de1e24783bbbdd6a2d3996be6f
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430101"
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

## <a name="set-up-azure-connector-for-sap-lama"></a>Configurar o conector do Azure para SAP LaMa

O conector do Azure é fornecido a partir do SAP LaMa 3,0 SP05. Recomendamos sempre instalar o pacote de suporte mais recente e o patch para o SAP LaMa 3,0. O conector do Azure usa uma entidade de serviço para autorizar contra Microsoft Azure. Siga estas etapas para criar uma entidade de serviço para o SAP Landscape Management (LaMa).

1. Ir para https://portal.azure.com
1. Abrir a folha Azure Active Directory
1. Clique em Registros de aplicativo
1. Clique em Adicionar
1. Insira um nome, selecione tipo de aplicativo "aplicativo Web/API", insira uma URL de logon (por exemplo, http: \//localhost) e clique em criar
1. A URL de logon não é usada e pode ser qualquer URL válida
1. Selecione o novo aplicativo e clique em chaves na guia Configurações
1. Insira uma descrição para uma nova chave, selecione "nunca expira" e clique em salvar
1. Anote o valor. Ele é usado como a senha para a entidade de serviço
1. Anote a ID do aplicativo. Ele é usado como o nome de usuário da entidade de serviço

A entidade de serviço não tem permissões para acessar os recursos do Azure por padrão. Você precisa conceder permissões de entidade de serviço para acessá-las.

1. Ir para https://portal.azure.com
1. Abra a folha grupos de recursos
1. Selecione o grupo de recursos que você deseja usar
1. Clique em controle de acesso (IAM)
1. Clique em Adicionar atribuição de função
1. Selecione o colaborador da função
1. Insira o nome do aplicativo que você criou acima
1. Clicar em Guardar
1. Repita a etapa de 3 a 8 para todos os grupos de recursos que você deseja usar no SAP LaMa

Abra o site do SAP LaMa e navegue até infraestrutura. Acesse a guia gerenciadores de nuvem e clique em Adicionar. Selecione o Adaptador de Nuvem de Microsoft Azure e clique em Avançar. Introduza as seguintes informações:

* Rótulo: escolha um nome para a instância do conector
* Nome de usuário: ID do aplicativo da entidade de serviço
* Senha: chave da entidade de serviço/senha
* URL: manter o @no__t padrão-0
* Intervalo de monitoramento (segundos): deve ser pelo menos 300
* ID da assinatura: ID da assinatura do Azure
* ID de locatário Azure Active Directory: ID do locatário Active Directory
* Host de proxy: nome de host do proxy se o SAP LaMa precisar de um proxy para se conectar à Internet
* Porta do proxy: porta TCP do proxy

Clique em configuração de teste para validar sua entrada. Você deve ver

Conexão bem-sucedida: a conexão com o Microsoft Cloud foi bem-sucedida. 7 grupos de recursos encontrados (somente 10 grupos solicitados)

na parte inferior do site.

## <a name="provision-a-new-adaptive-sap-system"></a>Provisionar um novo sistema SAP adaptável

Você pode implantar manualmente uma nova máquina virtual ou usar um dos modelos do Azure no [repositório de início rápido](https://github.com/Azure/azure-quickstart-templates). Ele contém modelos para [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [servidores de aplicativos SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)e o [banco de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Você também pode usar esses modelos para provisionar novos hosts como parte de uma cópia/clonagem do sistema, etc.

É recomendável usar uma sub-rede separada para todas as máquinas virtuais que você deseja gerenciar com o SAP LaMa e não usar endereços IP dinâmicos para impedir que o endereço IP "roubando" ao implantar novas máquinas virtuais e instâncias SAP sejam despreparadas.

> [!NOTE]
> Se possível, remova todas as extensões de máquina virtual, pois elas podem causar tempos de execução longos para desanexar discos de uma máquina virtual.

Verifique se o usuário \<hanasid > ADM, \<sapsid > ADM e os SAPs de grupo existem no computador de destino com a mesma ID e GID ou use LDAP. Habilite e inicie o servidor NFS nas máquinas virtuais que devem ser usadas para executar o SAP NetWeaver (A) SCS.

### <a name="manual-deployment"></a>Implantação manual

O SAP LaMa se comunica com a máquina virtual usando o agente de host do SAP. Se você implantar as máquinas virtuais manualmente ou não usar o modelo de Azure Resource Manager do repositório de início rápido, certifique-se de instalar o agente de host do SAP e as extensões adaptáveis do SAP mais recentes. Para obter mais informações sobre os níveis de patch necessários para o Azure, consulte a observação do SAP [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Implantação manual de uma máquina virtual Linux

Crie uma nova máquina virtual com um dos sistemas operacionais com suporte listados na observação SAP [2343511]. Adicione outras configurações de IP para as instâncias do SAP. Cada instância precisa de pelo menos no endereço IP e deve ser instalada usando um nome de host virtual.

A instância ASCS do SAP NetWeaver precisa de discos para/sapmnt/\<SAPSID >,/usr/SAP/\<SAPSID >,/usr/SAP/trans e/usr/SAP/\<sapsid > ADM. Os servidores de aplicativos SAP NetWeaver não precisam de discos adicionais. Tudo relacionado à instância do SAP deve ser armazenado no ASCS e exportado via NFS. Caso contrário, no momento, não é possível adicionar servidores de aplicativos adicionais usando o SAP LaMa.

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

* _artifactsLocation: o URI de base, em que os artefatos exigidos por esse modelo estão localizados. Quando o modelo é implantado usando os scripts que o acompanha, um local privado na assinatura será usado e esse valor será gerado automaticamente. Necessário apenas se você não implantar o modelo do GitHub.

* _artifactsLocationSasToken: o sasToken necessário para acessar o _artifactsLocation. Quando o modelo for implantado usando os scripts que o acompanha, um sasToken será gerado automaticamente. Necessário apenas se você não implantar o modelo do GitHub.

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

![Linux @ no__t-1 Linux  
Adicione o seguinte parâmetro de perfil ao perfil do agente de host do SAP, que está localizado em/usr/SAP/hostctrl/exe/host_profile. Para obter mais informações, consulte SAP Note [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Instalar o ASCS do SAP NetWeaver para SAP HANA no Azure NetAppFiles (seja) BETA

> [!NOTE]
> Essa funcionalidade ainda é nem GA. Para obter mais informações, consulte SAP Note [2815988] (visível somente para clientes de visualização).
Abra um incidente SAP no componente BC-VCM-LVM-HYPERV e solicite ingressar no adaptador de armazenamento LaMa para Azure NetApp Files visualização

O seja fornece NFS para Azure. No contexto do SAP LaMa, isso simplifica a criação de instâncias ASCS (ABAP central Services) e a instalação subsequente de servidores de aplicativos. Anteriormente, a instância ASCS tinha que agir como servidor NFS e o parâmetro acosprep/nfs_paths precisava ser adicionado à host_profile do SAP Hostagent.

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

Os volumes de NFS agora podem ser definidos. Como haverá volumes para vários sistemas em um pool, um esquema de nomenclatura autoexplicado deverá ser escolhido. Adicionar o SID ajuda a agrupar volumes relacionados juntos. Para o ASCS e a instância do as, as seguintes montagens são necessárias: */sapmnt/\<SID @ no__t-2*, */usr/SAP/\<SID @ no__t-5*e */Home/\<sid @ no__t-8adm*. Opcionalmente, */usr/SAP/trans* é necessário para o diretório de transporte central, que é pelo menos usado por todos os sistemas de um cenário.

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

pontos de montagem de @no__t 0SAP LaMa no LaMa ](media/lama/sap-lama-ascs.png) (este é um exemplo. Os endereços IP e o caminho de exportação são diferentes dos usados antes)


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

É recomendável usar o parâmetro de perfil do SAP NetWeaver bancos de HDB/hdb_use_ident para definir a identidade usada para localizar a chave no USERSTORE do HDB. Você pode adicionar esse parâmetro manualmente após a instalação da instância do banco de dados com SWPM ou executar SWPM com

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

#### <a name="install-sql-server"></a>Instalar SQL Server

Você precisa adicionar o endereço IP do nome de host virtual do banco de dados a uma interface de rede. A maneira recomendada é usar sapacext. Se você montar o endereço IP usando o sapacext, certifique-se de remontar o endereço IP após uma reinicialização.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Execute a instalação da instância do banco de dados do SWPM na máquina virtual do SQL Server. Use SAPINST_USE_HOSTNAME =*AS1-DB* para substituir o nome do host usado para se conectar ao SQL Server. Se você implantou a máquina virtual usando o modelo de Azure Resource Manager, certifique-se de definir o diretório usado para os arquivos de dados de banco de dados para *C:\sql\data* e o arquivo de log de banco de dados para *C:\sql\log*.

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
  * O [Driver de SQL Server ODBC] [SQL Server] A permissão SELECT foi negada no objeto ' log_shipping_primary_databases ', banco de dados ' msdb ', esquema ' dbo '. [SOAPFaultException]  
  A permissão SELECT foi negada no objeto ' log_shipping_primary_databases ', banco de dados ' msdb ', esquema ' dbo '.
  * Solução  
    Verifique se o *NT AUTHORITY\SYSTEM* pode acessar o SQL Server. Consulte a observação do SAP [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Erros e avisos de validação de instância

* Uma exceção foi gerada na validação do USERSTORE do HDB  
  * consulte o Visualizador de log  
    com. SAP. NW. LM. ACI. monitor. API. Validation. RuntimeValidationException: exceção no validador com a ID ' RuntimeHDBConnectionValidator ' (validação: ' VALIDATION_HDB_USERSTORE '): não foi possível recuperar o hdbuserstore  
    O USERSTORE do HANA não está no local correto
  * Solução  
    Verifique se o/usr/sap/AH1/hdbclient/install/installation.ini está correto

### <a name="errors-and-warnings-during-a-system-copy"></a>Erros e avisos durante uma cópia do sistema

* Ocorreu um erro ao validar a etapa de provisionamento do sistema
  * Causado por: com. SAP. NW. LM. ACI. Engine. base. API. util. Exception. HAOperationException chamando '/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 @ no__t-0status = 5 @ no__t-1port = 35013 PF =/usr/SAP/hostctrl/exe/host_ Perfil-R-T dev_lvminfo-u sistema-p gancho-R ' | /usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 @ no__t-2status = 5 @ no__t-3port = 35013 PF =/usr/SAP/hostctrl/exe/host_profile-R-T dev_lvminfo-u sistema-p Hook-R
  * Solução  
    Fazer backup de todos os bancos de dados no sistema HANA de origem

* *Início* da etapa de cópia do sistema da instância do banco de dados
  * Falha na operação ' 000D3A282BC91EE8A1D76CF1F92E2944 ' do agente de host (OperationException. FaultCode: ' 127 ', mensagem: ' falha na execução do comando. : [Microsoft] [driver ODBC SQL Server] [SQL Server] o usuário não tem permissão para alterar o banco de dados ' AS2 ', o banco de dados não existe ou o banco de dados não está em um estado que permita verificações de acesso. ')
  * Solução  
    Verifique se o *NT AUTHORITY\SYSTEM* pode acessar o SQL Server. Consulte a observação do SAP [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Erros e avisos durante um clone do sistema

* Erro ao tentar registrar o agente de instância na etapa *registro forçado e iniciar o agente de instância* do servidor de aplicativos ou ASCS
  * Erro ao tentar registrar o agente de instância. (RemoteException: ' falha ao carregar os dados da instância do perfil ' \\as1-ASCs \ sapmnt \ AS1 \ SYS \ Profile \ AS1_D00_as1-di-0 ': não é possível acessar o perfil ' \\as1-ASCs \ sapmnt \ AS1 \ SYS \ Profile \ AS1_D00_as1-di-0 ': nenhum arquivo ou diretório. ')
  * Solução  
   Verifique se o compartilhamento sapmnt no ASCS/SCS tem acesso completo para SAP_AS1_GlobalAdmin

* Erro na etapa *habilitar proteção de inicialização para clone*
  * Falha ao abrir o arquivo ' \\as1-ASCs \ sapmnt \ AS1 \ SYS \ Profile \ AS1_D00_as1-di-0 ' causa: não há tal arquivo ou diretório
  * Solução  
    A conta de computador do servidor de aplicativos precisa de acesso de gravação ao perfil

### <a name="errors-and-warnings-during-create-system-replication"></a>Erros e avisos durante a criação da replicação do sistema

* Exceção ao clicar em criar replicação do sistema
  * Causado por: com. SAP. NW. LM. ACI. Engine. base. API. util. Exception. HAOperationException chamando '/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 @ no__t-0status = 5 @ no__t-1port = 35013 PF =/usr/SAP/hostctrl/exe/host_ Perfil-R-T dev_lvminfo-u sistema-p gancho-R ' | /usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 @ no__t-2status = 5 @ no__t-3port = 35013 PF =/usr/SAP/hostctrl/exe/host_profile-R-T dev_lvminfo-u sistema-p Hook-R
  * Solução  
    Testar se sapacext pode ser executado como `<hanasid` > ADM

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
  * ERRO: (último erro relatado pela etapa: capturada ESAPinstException na chamada do módulo: validador da etapa ' | NW_DI | IND | IND | IND | IND | 0 | 0 | NW_GetSidFromProfiles | IND | IND | IND | IND | getSid | 0 | NW_readProfileDir | IND | IND | IND | IND | readProfile | 0 | getProfileDir ' relatou um erro: o nó \\ \ as1-ascs\sapmnt\AS1\SYS\profile não existe. Iniciar o SAPinst no modo interativo para resolver esse problema)
  * Solução  
    Verifique se o SWPM está em execução com um usuário que tem acesso ao perfil. Este usuário pode ser configurado no assistente de instalação do servidor de aplicativos

* Erro ao executar a etapa SAPinst: askUnicode
  * ERRO: (último erro relatado pela etapa: capturada ESAPinstException na chamada do módulo: validador da etapa ' | NW_DI | IND | IND | IND | IND | 0 | 0 | NW_GetSidFromProfiles | IND | IND | IND | IND | getSid | 0 | NW_getUnicode | IND | IND | IND | IND | Unicode | 0 | askUnicode ' relatou um erro: iniciar o SAPinst no modo interativo para resolver esse problema)
  * Solução  
    Se você usar um kernel SAP recente, o SWPM não poderá determinar se o sistema é mais um sistema Unicode usando o servidor de mensagens do ASCS. Consulte a observação do SAP [2445033] para obter mais detalhes.  
    Esse problema será corrigido em um novo pacote de suporte/patch do SAP LaMa.  
    Defina o parâmetro de perfil OS_UNICODE = UC no perfil padrão do seu sistema SAP para contornar esse problema.

* Erro ao executar a etapa SAPinst: dCheckGivenServer
  * Erro ao executar a etapa SAPinst: dCheckGivenServer "Version =" 1.0 "erro: (último erro relatado pela etapa: \<p > instalação foi cancelada pelo usuário. \</p >
  * Solução  
    Verifique se o SWPM está em execução com um usuário que tem acesso ao perfil. Este usuário pode ser configurado no assistente de instalação do servidor de aplicativos

* Erro ao executar a etapa SAPinst: checkClient
  * Erro ao executar a etapa SAPinst: checkClient "Version =" 1.0 "erro: (último erro relatado pela etapa: \<p > instalação foi cancelada pelo usuário. \</p >)
  * Solução  
    Verifique se o Microsoft ODBC driver for SQL Server está instalado na máquina virtual na qual você deseja instalar o servidor de aplicativos

* Erro ao executar a etapa SAPinst: copyScripts
  * Último erro relatado pela etapa: falha na chamada do sistema. DETALHES: erro 13 (0x0000000d) (permissão negada) na execução da chamada do sistema ' fopenU ' com parâmetro (\\ \ AS1-ASCs/sapmnt/AS1/SYS/exe/UC/NTAMD64/strdbs. cmd, w), linha (494) no arquivo (\ Bas/Bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/ syslib/FileSystem/syxxcfstrm2. cpp), rastreamento de pilha:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  CSiServiceSet. cpp: 63: CSiServiceSet:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: Call ()  
  iaxxcfile. cpp: 183: iastring CIaOsFileConnect:: callMemberFunction (iastring const & name, args_t const & args)  
  iaxxcfile. cpp: 1849: iastring CIaOsFileConnect:: newFileStream (args_t const & _args)  
  iaxxbfile. cpp: 773: CIaOsFile:: newFileStream_impl (4)  
  syxxcfile. cpp: 233: CSyFileImpl:: openStream (ISyFile:: eFileOpenMode)  
  syxxcfstrm. cpp: 29: CSyFileStreamImpl:: CSyFileStreamImpl (CSyFileStream *, iastring, ISyFile:: eFileOpenMode)  
  syxxcfstrm. cpp: 265: CSyFileStreamImpl:: Open ()  
  syxxcfstrm2. cpp: 58: CSyFileStream2Impl:: CSyFileStream2Impl (const CSyPath & \\ \ AW1-ASCs/sapmnt/AW1/SYS/exe/UC/NTAMD64/strdbs. cmd, 0x4)  
  syxxcfstrm2. cpp: 456: CSyFileStream2Impl:: Open ()
  * Solução  
    Verifique se o SWPM está em execução com um usuário que tem acesso ao perfil. Este usuário pode ser configurado no assistente de instalação do servidor de aplicativos

* Erro ao executar a etapa SAPinst: askPasswords
  * Último erro relatado pela etapa: falha na chamada do sistema. DETALHES: erro 5 (0x00000005) (acesso negado.) na execução da chamada do sistema ' NetValidatePasswordPolicy ' com o parâmetro (...), linha (359) no arquivo (\ Bas/Bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/Account/synxcaccmg. cpp), rastreamento de pilha:  
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
  iaxxcaccount. cpp: 107: iastring CIaOsAccountConnect:: callMemberFunction (iastring const & name, args_t const & args)  
  iaxxcaccount. cpp: 1186: iastring CIaOsAccountConnect:: validatePasswordPolicy (args_t const & _args)  
  iaxxbaccount. cpp: 430: CIaOsAccount:: validatePasswordPolicy_impl ()  
  synxcaccmg. cpp: 297: ISyAccountMgt::P asswordValidationMessage CSyAccountMgtImpl:: validatePasswordPolicy (saponazure, * * * * *) const)
  * Solução  
    Certifique-se de adicionar uma regra de host em etapa de *isolamento* para permitir a comunicação da VM para o controlador de domínio

## <a name="next-steps"></a>Passos seguintes
* [Manual de operações do SAP HANA no Azure][hana-ops-guide]
* [Planejamento e implementação de máquinas virtuais do Azure para SAP][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
