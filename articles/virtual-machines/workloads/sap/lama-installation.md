---
title: Conector SAP LaMa para Azure Microsoft Docs
description: Gestão de Sistemas SAP em Azure utilizando SAP LaMa
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
ms.openlocfilehash: fda62ff0af29c7cf681d9438b02420d299535701
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80293937"
---
# <a name="sap-lama-connector-for-azure"></a>Conector de SAP LaMa para o Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Declaração de Apoio Geral: Por favor, abra sempre um incidente com o SAP no componente BC-VCM-LVM-HYPERV se precisar de apoio para o SAP LaMa ou o conector Azure.

O SAP LaMa é utilizado por muitos clientes para operar e monitorizar a sua paisagem SAP. Desde o SAP LaMa 3.0 SP05, envia com um conector a Azure por defeito. Pode utilizar este conector para desalocar e iniciar máquinas virtuais, copiar e realojar discos geridos e eliminar discos geridos. Com estas operações básicas, pode realojar, copiar, clonar e refrescar sistemas SAP utilizando sAP LaMa.

Este guia descreve como configura o conector Azure para SAP LaMa, cria máquinas virtuais que podem ser usadas para instalar sistemas SAP adaptativos e como configurá-los.

> [!NOTE]
> O conector só está disponível na SAP LaMa Enterprise Edition

## <a name="resources"></a>Recursos

As seguintes Notas SAP estão relacionadas com o tema do SAP LaMa no Azure:

| Número de nota | Título |
| --- | --- |
| [2343511] |Conector Microsoft Azure para Gestão de Paisagens SAP (LaMa) |
| [2350235] |SAP Landscape Management 3.0 - Edição empresarial |

Leia também o Portal de [Ajuda SAP para SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Observações gerais

* Certifique-se de ativar a *criação automática de pontos* de montagem na configuração -> Configurações -> Motor  
  Se o SAP LaMa acumular volumes utilizando as extensões adaptativas SAP numa máquina virtual, o ponto de montagem deve existir se esta definição não estiver ativada.

* Utilize subnet separado e não utilize endereços IP dinâmicos para evitar que o endereço IP "roube" ao implementar novos VMs e instâncias SAP não estão preparados  
  Se utilizar a atribuição dinâmica de endereços IP na subnet, que também é utilizada pelo SAP LaMa, a preparação de um sistema SAP com SAP LaMa pode falhar. Se um sistema SAP não estiver preparado, os endereços IP não estão reservados e podem ser atribuídos a outras máquinas virtuais.

* Se você assinar em anfitriões geridos, certifique-se de não bloquear sistemas de ficheiros de não serem montados  
  Se iniciar sessão numa máquina virtual Linux e alterar o diretório de trabalho para um diretório num ponto de montagem, por exemplo/usr/sap/AH1/ASCS00/exe, o volume não pode ser desmontado e uma recolocação ou falha não-preparação.

* Certifique-se de que desativa as CLOUD_NETCONFIG_MANAGE nas máquinas virtuais SUSE SLES Linux. Para mais detalhes, consulte [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633).

## <a name="set-up-azure-connector-for-sap-lama"></a>Configurar o conector Azure para sap LaMa

O conector Azure é enviado a partir de SAP LaMa 3.0 SP05. Recomendamos sempre instalar o mais recente pacote de suporte e patch para SAP LaMa 3.0.

O conector Azure utiliza a API do Gestor de Recursos Azure para gerir os seus recursos Azure. O SAP LaMa pode utilizar um Diretor de Serviço ou uma Identidade Gerida para autenticar contra esta API. Se o seu SAP LaMa estiver a funcionar num VM Azure, recomendamos a utilização de uma Identidade Gerida, tal como descrito no capítulo [Use uma Identidade Gerida para ter acesso à API Azure](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d). Se pretender utilizar um Diretor de Serviço, siga os passos no capítulo Use um Diretor de [Serviço para ter acesso à API Azure](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e).

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Utilize um Diretor de Serviço para ter acesso à API Azure

O conector Azure pode utilizar um Diretor de Serviço para autorizar contra o Microsoft Azure. Siga estes passos para criar um Diretor de Serviço para gestão paisagística SAP (LaMa).

1. Ir para https://portal.azure.com
1. Abra a lâmina do Diretório Ativo Azure
1. Clique nos registos da App
1. Clique em Novo Registo
1. Insira um nome e clique no Registo
1. Selecione a nova App e clique em Certificados & segredos no separador Definições
1. Crie um novo segredo de cliente, insira uma descrição para uma nova chave, selecione quando o segredo deve expirar e clique em Guardar
1. Escreva o Valor. É usado como palavra-passe para o Diretor de Serviço
1. Escreva o ID da inscrição. É usado como o nome de utilizador do Diretor de Serviço

O Diretor de Serviço não tem permissões para aceder aos seus recursos Azure por defeito. Tens de dar permissões ao Diretor de Serviço para aceder a elas.

1. Ir para https://portal.azure.com
1. Abra a lâmina dos grupos de recursos
1. Selecione o grupo de recursos que pretende utilizar
1. Clique no controlo de acesso (IAM)
1. Clique em Adicionar atribuição de funções
1. Selecione o papel Contribuinte
1. Insira o nome da aplicação que criou acima
1. Clicar em Guardar
1. Repita o passo 3 a 8 para todos os grupos de recursos que pretende utilizar no SAP LaMa

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Utilize uma Identidade Gerida para ter acesso à API Azure

Para poder utilizar uma Identidade Gerida, a sua instância SAP LaMa tem de funcionar num VM Azure que tenha um sistema ou identidade atribuída ao utilizador. Para obter mais informações sobre identidades geridas, leia O [Configure managed identities for Azure resources on a VM using the Azure portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) [que é gerida identidades para recursos Azure?](../../../active-directory/managed-identities-azure-resources/overview.md)

A Identidade Gerida não tem permissões para aceder aos seus recursos Azure por defeito. Tens de lhe dar permissões para aceder a eles.

1. Ir para https://portal.azure.com
1. Abra a lâmina dos grupos de recursos
1. Selecione o grupo de recursos que pretende utilizar
1. Clique no controlo de acesso (IAM)
1. Clique em Adicionar -> Adicionar Tarefa de Função
1. Selecione o papel Contribuinte
1. Selecione 'Máquina Virtual' para 'Atribuir acesso a'
1. Selecione a máquina virtual onde a sua instância SAP LaMa está em execução
1. Clicar em Guardar
1. Repita os passos para todos os grupos de recursos que pretende utilizar no SAP LaMa

Na configuração do conector SAP LaMa Azure, selecione 'Use Identidade Gerida' para permitir a utilização da Identidade Gerida. Se pretender utilizar uma identidade atribuída ao sistema, certifique-se de deixar o campo Nome do Utilizador vazio. Se pretender utilizar uma identidade atribuída ao utilizador, introduza o id de identidade atribuído ao utilizador no campo Nome do Utilizador.

### <a name="create-a-new-connector-in-sap-lama"></a>Criar um novo conector em SAP LaMa

Abra o site da SAP LaMa e navegue para infraestruturas. Vá a separador Cloud Managers e clique em Adicionar. Selecione o Adaptador cloud Microsoft Azure e clique em Seguinte. Introduza as seguintes informações:

* Etiqueta: Escolha um nome para a instância do conector
* Nome do utilizador: Id de aplicação principal de serviço ou ID do utilizador designado identidade da máquina virtual. Consulte [Utilização de um Sistema ou Identidade Atribuída ao Utilizador] para obter mais informações
* Palavra-passe: Chave/senha principal de serviço. Pode deixar este campo vazio se utilizar um sistema ou identidade atribuída ao utilizador.
* URL: Manter o padrão`https://management.azure.com/`
* Intervalo de monitorização (Segundos): Deve ser pelo menos 300
* Utilizar identidade gerida: O SAP LaMa pode utilizar um sistema ou identidade atribuída ao utilizador para autenticar contra a API Azure. Ver capítulo [Utilize uma Identidade Gerida para ter acesso à API Azure](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) neste guia.
* ID de subscrição: ID de subscrição do Azure
* Id do inquilino do Diretório Ativo Azure: ID do inquilino do Diretório Ativo
* Hospedeiro proxy: Nome de anfitrião do proxy se SAP LaMa precisar de um proxy para ligar à internet
* Proxy port: Porto TCP do proxy
* Alterar o Tipo de Armazenamento para economizar custos: Ative esta definição se o Adaptador Azure alterar o tipo de armazenamento dos Discos Geridos para economizar custos quando os discos não estiverem a ser utilizados. Para os discos de dados referenciados numa configuração de instância SAP, o adaptador mudará o tipo de disco para O Armazenamento Padrão durante uma instância não preparada e de volta ao tipo de armazenamento original durante uma preparação por exemplo. Se parar uma máquina virtual no SAP LaMa, o adaptador alterará o tipo de armazenamento de todos os discos anexados, incluindo o disco OS para o Armazenamento Padrão. Se iniciar uma máquina virtual no SAP LaMa, o adaptador mudará o tipo de armazenamento de volta para o tipo de armazenamento original.

Clique na Configuração do Teste para validar a sua entrada. Deveria ver.

Ligação bem sucedida: A ligação à nuvem da Microsoft foi bem sucedida. 7 grupos de recursos encontrados (apenas 10 grupos solicitados)

na parte inferior do site.

## <a name="provision-a-new-adaptive-sap-system"></a>Fornecer um novo sistema Adaptativo SAP

Pode implantar manualmente uma nova máquina virtual ou utilizar um dos modelos Azure no [repositório de arranque rápido](https://github.com/Azure/azure-quickstart-templates). Contém modelos para [sAP NetWeaver ASCS,](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs)servidores de [aplicações SAP NetWeaver,](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)e a base de [dados](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Também pode utilizar estes modelos para fornecer novos anfitriões como parte de uma cópia/clone do sistema, etc.

Recomendamos a utilização de uma subnet separada para todas as máquinas virtuais que pretende gerir com o SAP LaMa e não utilize endereços IP dinâmicos para evitar que o endereço IP "roube" ao implementar novas máquinas virtuais e as instâncias SAP não estão preparadas.

> [!NOTE]
> Se possível, remova todas as extensões da máquina virtual, pois podem causar longos prazos de execução para separar discos de uma máquina virtual.

Certifique-se \<de que existem \<saisys de utilizador hanasid>adm, sapsid>adm e group sapsys na máquina-alvo com o mesmo ID e gid ou utilize LDAP. Ative e ligue o servidor NFS nas máquinas virtuais que devem ser utilizadas para executar o SAP NetWeaver (A)SCS.

### <a name="manual-deployment"></a>Implantação Manual

O SAP LaMa comunica com a máquina virtual utilizando o Agente hospedeiro SAP. Se utilizar manualmente as máquinas virtuais ou não utilizando o modelo do Gestor de Recursos Azure a partir do repositório de arranque rápido, certifique-se de instalar o mais recente Agente hospedeiro SAP e as extensões adaptativas SAP. Para obter mais informações sobre os níveis de patch necessários para o Azure, consulte A Nota [SAP 2343511 .]

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Implantação manual de uma máquina virtual Linux

Crie uma nova máquina virtual com um dos sistemas de operação suportados listados na Nota [2343511]. Adicione configurações IP adicionais para as instâncias SAP. Cada instância necessita, pelo menos, no endereço IP e deve ser instalada com um nome de anfitrião virtual.

A instância SAP NetWeaver ASCS precisa de discos\<para /sapmnt/ SAPSID>, /usr/sap/\<SAPSID>, /usr/sap/trans, e /usr/sap/sapsid\<>adm. Os servidores de aplicações SAP NetWeaver não precisam de discos adicionais. Tudo o que diz respeito à instância SAP deve ser armazenado no ASCS e exportado através de NFS. Caso contrário, não é atualmente possível adicionar servidores de aplicações adicionais utilizando o SAP LaMa.

![SAP NetWeaver ASCS em Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Implementação manual para SAP HANA

Crie uma nova máquina virtual com um dos sistemas de operação suportados para o SAP HANA, tal como listado na Nota [2343511]. Adicione uma configuração IP adicional para SAP HANA e uma por inquilino HANA.

SAP HANA precisa de discos para /hana/shared, /hana/backup, /hana/data, e /hana/log

![SAP HANA em Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Implementação manual para Base de Dados Oracle em Linux

Crie uma nova máquina virtual com um dos sistemas de funcionamento suportados para bases de dados Oracle, tal como listado na Nota [SAP 2343511]. Adicione uma configuração IP adicional para a base de dados Oracle.

A base de dados oracle precisa de discos para /oráculo, /casa/oraod1, e /casa/oráculo

![Base de dados oracle em Linux](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Implementação manual para microsoft SQL Server

Crie uma nova máquina virtual com um dos sistemas de operação suportados para o Microsoft SQL Server, listado no SAP Note [2343511]. Adicione uma configuração IP adicional para a instância do Servidor SQL.

O servidor de base de dados do SQL Server precisa de discos para os dados da base de dados e ficheiros de registo e discos para c:\usr\sap.

![Base de dados oracle em Linux](media/lama/sap-lama-db-sql.png)

Certifique-se de instalar um controlador Microsoft ODBC suportado para o SQL Server numa máquina virtual que pretende utilizar para realojar um servidor de aplicação SAP NetWeaver para ou como um alvo de cópia/clone do sistema.

O SAP LaMa não pode realojar o Próprio SQL Server de modo a que uma máquina virtual que deseja utilizar para realojar uma instância de base de dados para ou como um alvo de cópia/clone do sistema precisa de SQL Server pré-instalado.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Implementar máquina virtual usando um modelo azure

Descarregue os seguintes arquivos disponíveis do [SAP Software Marketplace](https://support.sap.com/swdc) para o sistema operativo das máquinas virtuais:

1. SAPCAR 7.21
1. Agente hospedeiro SAP 7.21
1. EXTENSÃO ADAPTIva SAP 1.0 EXT

Também descarregue os seguintes componentes do [Microsoft Download Center](https://www.microsoft.com/download)

1. Microsoft Visual C++ 2010 Pacote Redistribuível (x64) (apenas windows)
1. Controlador Microsoft ODBC para servidor SQL (apenas SQL Server)

Os componentes são necessários para implantar o modelo. A maneira mais fácil de disponibilizá-los ao modelo é carregá-los para uma conta de armazenamento Azure e criar uma Assinatura de Acesso Partilhado (SAS).

Os modelos têm os seguintes parâmetros:

* sapSystemId: O ID do sistema SAP. É utilizado para criar o layout do disco (por\<exemplo/usr/seiva/ sapsid>).

* nome do computador: O nome do computador da nova máquina virtual. Este parâmetro também é utilizado pela SAP LaMa. Quando utiliza este modelo para fornecer uma nova máquina virtual como parte de uma cópia do sistema, o SAP LaMa aguarda até que o anfitrião com este nome de computador possa ser alcançado.

* osTipo: O tipo do sistema operativo que pretende implementar.

* dbtype: O tipo da base de dados. Este parâmetro é usado para determinar quantas configurações IP adicionais precisam de ser adicionadas e como deve ser o layout do disco.

* sapSystemSize: O tamanho do Sistema SAP que pretende implementar. É utilizado para determinar o tipo e o tamanho da instância da máquina virtual.

* adminUsername: Username para a máquina virtual.

* adminPassword: Palavra-passe para a máquina virtual. Também pode fornecer uma chave pública para o SSH.

* sshKeyData: Chave SSH pública para as máquinas virtuais. Apenas suportado para sistemas operativos Linux.

* subnetId: A identificação da subnet que pretende utilizar.

* implementaçãoEmptyTarget: Pode implementar um alvo vazio se pretender utilizar a máquina virtual como alvo, por exemplo, de relocalização ou similar. Neste caso, não estão ligados discos adicionais ou configurações IP.

* sapcarLocalização: A localização da aplicação sapcar que corresponde ao sistema operativo que implementa. a sapcar é utilizada para extrair os arquivos que fornece noutros parâmetros.

* sapHostAgentArchiveLocation: A localização do arquivo do Agente de Acolhimento SAP. O Agente hospedeiro SAP é implantado como parte deste modelo de implantação.

* sapacExtLocation: A localização das extensões adaptativas SAP. SAP Nota [2343511] lista o nível mínimo de patch necessário para o Azure.

* vcRedistLocalização: A localização do tempo de execução do VC que é necessário para instalar as extensões adaptivas SAP. Este parâmetro só é necessário para windows.

* odbcDriverLocation: A localização do controlador ODBC que pretende instalar. Apenas o controlador Microsoft ODBC para o SQL Server é suportado.

* sapadmPassword: A palavra-passe para o utilizador do sapadm.

* sapadmId: O ID do utilizador Linux do utilizador do sapadm. Não é necessário para o Windows.

* sapsysGid: A identificação do grupo Linux do grupo sapsys. Não é necessário para o Windows.

* _artifactsLocation: A base URI, onde estão localizados artefactos exigidos por este modelo. Quando o modelo for implantado utilizando os scripts que o acompanham, será utilizada uma localização privada na subscrição e este valor será automaticamente gerado. Só é necessário se não implementar o modelo do GitHub.

* _artifactsLocationSasToken: O sasToken necessário para aceder _artifactsLocation. Quando o modelo é implantado utilizando os scripts que o acompanham, um sasToken será automaticamente gerado. Só é necessário se não implementar o modelo do GitHub.

### <a name="sap-hana"></a>SAP HANA

Nos exemplos abaixo, assumimos que instala o SAP HANA com o sistema ID HN1 e o sistema SAP NetWeaver com o sistema ID AH1 do sistema. Os nomes de anfitriões virtuais são hn1-db para a instância HANA, ah1-db para o inquilino HANA usado pelo sistema SAP NetWeaver, ah1-ascs para o SAP NetWeaver ASCS e ah1-di-0 para o primeiro servidor de aplicação SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Instale o SAP NetWeaver ASCS para SAP HANA utilizando discos geridos azure

Antes de iniciar o SAP Software Provisioning Manager (SWPM), tem de montar o endereço IP do nome de anfitrião virtual do ASCS. A forma recomendada é usar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se de que volta a montar o endereço IP após uma reinicialização.

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

Executar SWPM e utilizar *ah1-ascs* para o nome de anfitrião da *instância ASCS*.

![Linux][Logo_Linux] Linux  
Adicione o seguinte parâmetro de perfil ao perfil do Agente hospedeiro SAP, que está localizado em /usr/sap/hostctrl/exe/host_profile. Para mais informações, consulte A Nota [SAP 2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Instale o SAP NetWeaver ASCS para SAP HANA no Azure NetAppFiles (ANF) BETA

> [!NOTE]
> Esta funcionalidade ainda não é GA. Para mais informações consulte o SAP Note [2815988] (apenas visível para pré-visualizar os clientes).
Abra um incidente SAP no componente BC-VCM-LVM-HYPERV e solicite a adesão ao adaptador de armazenamento LaMa para a pré-visualização de Ficheiros Azure NetApp

A ANF fornece NFS para o Azure. No contexto do SAP LaMa isto simplifica a criação dos casos de Serviços Centrais (ASCS) da ABAP e a subsequente instalação de servidores de aplicações. Anteriormente, a instância ASCS também tinha de funcionar como servidor NFS e o parâmetro acosprep/nfs_paths teve de ser adicionado ao host_profile do Hostagent SAP.

#### <a name="anf-is-currently-available-in-these-regions"></a>A ANF está atualmente disponível nestas regiões:

Austrália Leste, Eua Central, Leste dos EUA, Leste dos EUA 2, Norte da Europa, Centro-Sul dos EUA, Europa Ocidental e Oeste dos EUA 2.

#### <a name="network-requirements"></a>Requisitos de rede

A ANF requer uma sub-rede delegada que deve fazer parte do mesmo VNET que os servidores SAP. Aqui está um exemplo para tal configuração.
Este ecrã mostra a criação do VNET e a primeira subnet:

![SAP LaMa cria rede virtual para a Azure ANF ](media/lama/sap-lama-createvn-50.png)

O próximo passo cria a subnet delegada para microsoft.NetApp/volumes.

![SAP LaMa adiciona subnet delegado ](media/lama/sap-lama-addsubnet-50.png)

![Lista de subnets SAP LaMa ](media/lama/sap-lama-subnets.png)

Agora é necessário criar uma conta NetApp dentro do portal Azure:

![SAP LaMa cria conta NetApp ](media/lama/sap-lama-create-netappaccount-50.png)

![Conta SAP LaMa NetApp criada ](media/lama/sap-lama-netappaccount.png)

Dentro da conta NetApp, o conjunto de capacidades especifica o tamanho e o tipo de discos para cada piscina:

![SAP LaMa cria piscina de capacidade NetApp ](media/lama/sap-lama-capacitypool-50.png)

![Piscina de capacidade SAP LaMa NetApp criada ](media/lama/sap-lama-capacitypool-list.png)

Os volumes NFS podem agora ser definidos. Uma vez que haverá volumes para vários sistemas numa piscina, deve ser escolhido um esquema de nomeação autoexplicado. A adição do SID ajuda a agrupar volumes relacionados. Para o ASCS e a instância AS são necessários os seguintes suportes: */sapmnt/\<SID,\>* */usr/seiva/\<SID\>*, e */home/\<sid\>adm*. Opcionalmente, é necessário */usr/sap/trans* para o diretório central de transportes, que é pelo menos utilizado por todos os sistemas de uma paisagem.

> [!NOTE]
> Durante a fase BETA, o nome dos volumes deve ser único dentro da subscrição.

![SAP LaMa criar um volume 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa criar um volume 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa criar um volume 3 ](media/lama/sap-lama-createvolume3-80.png)

Estes passos também têm de ser repetidos para os outros volumes.

![Lista sap LaMa de volumes criados ](media/lama/sap-lama-volumes.png)

Agora, estes volumes precisam de ser montados nos sistemas onde será realizada a instalação inicial com o SAP SWPM.

Primeiro, os pontos de montagem têm de ser criados. Neste caso, o SID é AN1, pelo que os seguintes comandos devem ser executados:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Em seguida, os volumes ANF serão montados com os seguintes comandos:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
Os comandos de montagem também podem ser derivados do portal. Os pontos de montagem locais precisam de ser ajustados.

Utilize o comando df-h para verificar.

![SAP LaMa montam pontos de osso nível ](media/lama/sap-lama-mounts.png)

Agora a instalação com SWPM deve ser realizada.

Os mesmos passos devem ser efetuados para pelo menos um exemplo AS.

Após a instalação bem sucedida, o sistema deve ser descoberto dentro do SAP LaMa.

Os pontos de montagem devem ser assim para o ASCS e a instância AS:

![Pontos de montagem SAP LaMa em LaMa ](media/lama/sap-lama-ascs.png) (Este é um exemplo. Os endereços IP e o caminho de exportação são diferentes dos utilizados anteriormente)


#### <a name="install-sap-hana"></a>Instalar o SAP HANA

Se instalar o SAP HANA utilizando o hdblcm da ferramenta de linha de comando, utilize o nome de guarda-parâmetro para fornecer um nome de anfitrião virtual. É necessário adicionar o endereço IP do nome de anfitrião virtual da base de dados a uma interface de rede. A forma recomendada é usar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se de que volta a montar o endereço IP após uma reinicialização.

Adicione outro nome de anfitrião virtual e endereço IP para o nome que é usado pelos servidores de aplicações para ligar ao inquilino HANA.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Executar a instalação da instância de base de dados de SWPM na máquina virtual do servidor de aplicações, e não na máquina virtual HANA. Utilize *ah1-db* para o *Anfitrião* da Base de Dados na Base de Dados de Diálogo *para sistema SAP*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Instale o servidor de aplicação SAP NetWeaver para SAP HANA

Antes de iniciar o SAP Software Provisioning Manager (SWPM), tem de montar o endereço IP do nome de anfitrião virtual do servidor de aplicações. A forma recomendada é usar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se de que volta a montar o endereço IP após uma reinicialização.

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

Recomenda-se utilizar o parâmetro de perfil SAP NetWeaver dbs/hdb/hdb_use_ident para definir a identidade que é usada para encontrar a chave na userstore HDB. Pode adicionar este parâmetro manualmente após a instalação da instância de base de dados com SWPM ou executar SWPM com

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Se o ajustar manualmente, também precisa de criar novas entradas na loja de utilizadores HDB.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Utilize *ah1-di-0* para o nome do *anfitrião* pas instância em dialog *primary application server Instance*.

#### <a name="post-installation-steps-for-sap-hana"></a>Etapas de pós-instalação para SAP HANA

Certifique-se de fazer o backback do SYSTEMDB e todas as bases de dados dos inquilinos antes de tentar fazer uma cópia do inquilino, mover-se de inquilino ou criar uma replicação do sistema.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

Nos exemplos abaixo, assumimos que instala o sistema SAP NetWeaver com ID do sistema AS1. Os nomes de anfitriões virtuais são as1-db para a instância SQL Server utilizada pelo sistema SAP NetWeaver, as1-ascs para o SAP NetWeaver ASCS e as1-di-0 para o primeiro servidor de aplicação SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Instale sAP NetWeaver ASCS para servidor SQL

Antes de iniciar o SAP Software Provisioning Manager (SWPM), tem de montar o endereço IP do nome de anfitrião virtual do ASCS. A forma recomendada é usar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se de que volta a montar o endereço IP após uma reinicialização.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Executar SWPM e utilizar *as1-ascs* para o nome de anfitrião da *instância ASCS*.

#### <a name="install-sql-server"></a>Instalar o SQL Server

É necessário adicionar o endereço IP do nome de anfitrião virtual da base de dados a uma interface de rede. A forma recomendada é usar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se de que volta a montar o endereço IP após uma reinicialização.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Executar a instalação da instância de base de dados de SWPM na máquina virtual do servidor SQL. Utilize SAPINST_USE_HOSTNAME=*as1-db* para anular o nome de anfitrião usado para ligar ao SQL Server. Se implementou a máquina virtual utilizando o modelo do Gestor de Recursos Azure, certifique-se de definir o diretório utilizado para os ficheiros de dados da base de dados para *C:\sql\data* e ficheiro de registo de base de dados para *C:\sql\log*.

Certifique-se de que o utilizador *NT AUTHORITY\SYSTEM* tem acesso ao Servidor SQL e tem a função de servidor *sysadmin*. Para mais informações, consulte SAP Nota [1877727] e [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Instalar servidor de aplicação SAP NetWeaver

Antes de iniciar o SAP Software Provisioning Manager (SWPM), tem de montar o endereço IP do nome de anfitrião virtual do servidor de aplicações. A forma recomendada é usar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se de que volta a montar o endereço IP após uma reinicialização.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Utilize *as1-di-0* para o nome do *anfitrião* pas instância na *instância do servidor de aplicações primárias*de dialog .

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="errors-and-warnings-during-discover"></a>Erros e Avisos durante a Descoberta

* A permissão SELECT foi negada
  * [Microsoft] [ODBC SQL Server Driver] [SQL Server] A permissão SELECT foi negada no objeto 'log_shipping_primary_databases', base de dados 'msdb', schema 'dbo'. [SOAPFaultException]  
  A permissão SELECT foi negada no objeto 'log_shipping_primary_databases', base de dados 'msdb', schema 'dbo'.
  * Solução  
    Certifique-se de que *o NT AUTHORITY\SYSTEM* pode aceder ao Servidor SQL. Ver Nota SAP [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Erros e Avisos para Validação por Exemplo

* Foi levantada uma exceção na validação da userstore HDB  
  * ver Visualizador de Registo  
    com.sap.nw.lm.aci.monitor.api.validação.RuntimeValidação: Exceção em validador com ID 'RuntimeHDBConnectionValidator' (Validação: 'VALIDATION_HDB_USERSTORE'): Não conseguiu recuperar a hdbuserstore  
    A loja de utilizadores HANA não está no local correto
  * Solução  
    Certifique-se de que /usr/sap/AH1/hdbclient/install/installation.ini está correto

### <a name="errors-and-warnings-during-a-system-copy"></a>Erros e Avisos durante uma Cópia do Sistema

* Ocorreu um erro ao validar o passo de provisionamento do sistema
  * Causada por: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o nível=0\;status=5\;porta=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;porta=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Solução  
    Pegue a cópia de segurança de todas as bases de dados no sistema HANA de origem

* System Copy Step *Start* da instância da base de dados
  * Operação agente de acolhimento '000D3A282 BC91E8A1D76CF1F92E2944' falhou (Operação Exceção. Código de avaria: '127', Mensagem: 'Execução de comando falhou. : [Microsoft][ODBC SQL Server Driver][SQL Server]O utilizador não tem permissão para alterar a base de dados 'AS2', a base de dados não existe, ou a base de dados não está num estado que permite verificações de acesso.')
  * Solução  
    Certifique-se de que *o NT AUTHORITY\SYSTEM* pode aceder ao Servidor SQL. Ver Nota SAP [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Erros e Advertências durante um Clone do Sistema

* Erro ocorreu ao tentar registar agente de instância em passo *Deregisto Forçado e Iniciar Agente* de Instância de servidor de aplicações ou ASCS
  * Erro ocorreu ao tentar registar o agente da instância. (RemoteException: 'Failed to load dados\\de instância sapmnt as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Não pode aceder ao perfil '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': No tal ficheiro ou diretório.')
  * Solução  
   Certifique-se de que a parte sapmnt no ASCS/SCS tem acesso total para SAP_AS1_GlobalAdmin

* Erro na etapa permite a proteção do *arranque para clone*
  * Falha na abertura\\do ficheiro ' as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' Causa: Não existe tal ficheiro ou diretório
  * Solução  
    A conta de computador do servidor de aplicações precisa de escrever acesso ao perfil

### <a name="errors-and-warnings-during-create-system-replication"></a>Erros e avisos durante a replicação do sistema de criação

* Exceção ao clicar na replicação do sistema de criar
  * Causada por: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o nível=0\;status=5\;porta=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;porta=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Solução  
    Teste se o sapacext `<hanasid` pode ser executado como>adm

* Erro quando a cópia completa não está ativada no Passo de Armazenamento
  * Ocorreu um erro ao reportar uma mensagem de atributo de contexto para o caminho IStorageCopyData.storageVolumeCopyList:1 e field targetStorageSystemId
  * Solução  
    Ignore os avisos em passo e tente novamente. Esta questão será corrigida num novo pacote/patch de suporte da SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Erros e Avisos durante a Mudança

* O caminho '/usr/sap/AH1' não é permitido para reexportações de nfs.
  * Verifique o SAP Nota [2628497] para obter mais detalhes.
  * Solução  
    Adicione as exportações ASCS ao Perfil de HostAgent ASCS. Ver Nota SAP [2628497]

* Função não implementada ao deslocalizar ASCS
  * Saída de comando: exportfs: host:/usr/sap/AX1: Função não implementada
  * Solução  
    Certifique-se de que o serviço de servidor NFS está ativado na máquina virtual do alvo de relocalização

### <a name="errors-and-warnings-during-application-server-installation"></a>Erros e avisos durante a instalação do servidor de aplicações

* Erro executando passo SAPinst: getProfileDir
  * ERRO: (Último erro relatado pelo passo: Caught ESAPinstException in module call: Validator of step '[ NW_DI ind[ind], ind[ind], 0. NW_GetSidFromProfiles ind[ind], ind[ind], NW_readProfileDirind[ind[ind[ind[ind[ind[ind[ind[ind[ind]readProfile[0[getProfileDir' relatou um erro: O nó \\\as1-ascs\sapmnt\AS1\SYS\perfil não existe. Inicie o SAPinst em modo interativo para resolver este problema)
  * Solução  
    Certifique-se de que o SWPM está a funcionar com um utilizador que tem acesso ao perfil. Este utilizador pode ser configurado no assistente de instalação do servidor de aplicações

* Erro de execução sAPinst passo: askUnicode
  * ERRO: (Último erro relatado pelo passo: Caught ESAPinstException in module call: Validator of step '[ NW_DI ind[ind], ind[ind], 0. NW_GetSidFromProfiles ind[ind], ind[ind], NW_getUnicode ind[ind[ind[ind[ind[ind[ind[ind[ind], ind[ind], relatou um erro: Iniciar o SAPinst em modo interativo para resolver este problema)
  * Solução  
    Se utilizar um núcleo SAP recente, o SWPM não pode determinar se o sistema é mais um sistema unicódigo utilizando o servidor de mensagens do ASCS. Consulte a Nota [SAP 2445033] para mais detalhes.  
    Esta questão será corrigida num novo pacote/patch de suporte da SAP LaMa.  
    Defina o parâmetro de perfil OS_UNICODE=uc no perfil predefinido do seu sistema SAP para contornar este problema.

* Error executando o passo SAPinst: dCheckGivenServer
  * Erro executando o passo SAPinst: dCheckGivenServer" versão="1.0" ERROR: (Último erro relatado pelo passo: \<p> Instalação foi cancelada pelo utilizador. \</p>
  * Solução  
    Certifique-se de que o SWPM está a funcionar com um utilizador que tem acesso ao perfil. Este utilizador pode ser configurado no assistente de instalação do servidor de aplicações

* Erro executando passo SAPinst: checkClient
  * Erro de execução SAPinst step: checkClient versão="1.0" ERROR: \<(Último erro reportado pelo passo: p> Instalação foi cancelada pelo utilizador. \</p>)
  * Solução  
    Certifique-se de que o controlador Microsoft ODBC para o SQL Server está instalado na máquina virtual na qual pretende instalar o servidor de aplicações

* Erro executando passo SAPinst: copyScripts
  * Último erro relatado pelo passo: a chamada do sistema falhou. DETALHES: Erro 13 (0x00000000d) (Permissão negada) na execução da\\chamada do sistema 'fopenU' com parâmetro (\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), linha (494) em ficheiro (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), traço de pilha:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executarService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::executar()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction (iastring const& name, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream (ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl (CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl (const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Solução  
    Certifique-se de que o SWPM está a funcionar com um utilizador que tem acesso ao perfil. Este utilizador pode ser configurado no assistente de instalação do servidor de aplicações

* Erro de execução sAPinst passo: askPasswords
  * Último erro relatado pelo passo: a chamada do sistema falhou. DETALHES: Erro 5 (0x000000005) (O acesso é negado.) na execução da chamada de sistema 'NetValidatePasswordPolicy' com parâmetro (...), linha (359) em ficheiro (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), traço de pilha:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executarService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::executar()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction (iastring const& name, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validarPasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidaçãoMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,****) const )
  * Solução  
    Certifique-se de adicionar uma regra de anfitrião em passo *Isolamento* para permitir a comunicação do VM ao controlador de domínio

## <a name="next-steps"></a>Passos seguintes
* [Manual de operações do SAP HANA no Azure][hana-ops-guide]
* [Planeamento e implementação de Máquinas Virtuais Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP][dbms-guide]
