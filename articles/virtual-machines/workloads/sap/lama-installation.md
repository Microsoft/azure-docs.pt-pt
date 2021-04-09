---
title: Conector SAP LaMa para Azure | Microsoft Docs
description: Gerir sistemas SAP em Azure usando SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: 4772fdae06f23430d829fa411068b7af7a85b3dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668712"
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
> Declaração geral de apoio: Abra sempre um incidente com o SAP sobre o componente BC-VCM-LVM-HYPERV se precisar de suporte para o SAP LaMa ou para o conector Azure.

O SAP LaMa é utilizado por muitos clientes para operar e monitorizar a sua paisagem SAP. Desde o SAP LaMa 3.0 SP05, envia um conector para Azure por defeito. Pode utilizar este conector para transar e iniciar máquinas virtuais, copiar e realojar discos geridos e eliminar discos geridos. Com estas operações básicas, pode realocar, copiar, clonar e refrescar os sistemas SAP usando o SAP LaMa.

Este guia descreve como configura o conector Azure para SAP LaMa, cria máquinas virtuais que podem ser usadas para instalar sistemas SAP adaptativos e como configurá-los.

> [!NOTE]
> O conector só está disponível na SAP LaMa Enterprise Edition

## <a name="resources"></a>Recursos

As seguintes Notas SAP estão relacionadas com o tema SAP LaMa on Azure:

| Número de nota | Título |
| --- | --- |
| [2343511] |Conector Microsoft Azure para gestão de paisagem SAP (LaMa) |
| [2350235] |SAP Gestão paisagística 3.0 - Edição empresarial |

Leia também o [Portal de Ajuda SAP para SAP LaMa.](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE)

## <a name="general-remarks"></a>Observações gerais

* Certifique-se de que ativa a *criação automática de pontos* de montagem em configurações -> -> motor  
  Se o SAP LaMa montar volumes utilizando as extensões adaptativas SAP numa máquina virtual, o ponto de montagem deve existir se esta regulação não estiver ativada.

* Utilize sub-redes separadas e não utilize endereços IP dinâmicos para evitar que o endereço IP "roube" ao implementar novos VMs e casos SAP não estejam preparados  
  Se utilizar a atribuição dinâmica de endereços IP na sub-rede, que também é utilizada pela SAP LaMa, preparar um sistema SAP com SAP LaMa pode falhar. Se um sistema SAP não estiver preparado, os endereços IP não são reservados e podem ser atribuídos a outras máquinas virtuais.

* Se iniciar sessão para anfitriões geridos, certifique-se de que não bloqueia os sistemas de ficheiros de serem desmontados  
  Se iniciar sessão numa máquina virtual Linux e mudar o diretório de trabalho para um diretório num ponto de montagem, por exemplo /usr/seiva/AH1/ASCS00/exe, o volume não pode ser desmontado e um realocamento ou falha.

* Certifique-se de desativar CLOUD_NETCONFIG_MANAGE em máquinas virtuais SUSE SLES Linux. Para mais detalhes, consulte [sUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633).

## <a name="set-up-azure-connector-for-sap-lama"></a>Configurar o conector Azure para SAP LaMa

O conector Azure é enviado a partir de SAP LaMa 3.0 SP05. Recomendamos sempre a instalação do mais recente pacote de suporte e patch para SAP LaMa 3.0.

O conector Azure utiliza a API do Gestor de Recursos Azure para gerir os seus recursos Azure. O SAP LaMa pode utilizar um Diretor de Serviço ou uma Identidade Gerida para autenticar contra esta API. Se o seu SAP LaMa estiver a funcionar num VM Azure, recomendamos a utilização de uma Identidade Gerida, conforme descrito no capítulo [Use uma Identidade Gerida para ter acesso à API Azure](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d). Se pretender utilizar um Diretor de Serviço, siga os passos do capítulo [Use a Service Principal para ter acesso à API Azure](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e).

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Utilize um Diretor de Serviço para ter acesso à AZure API

O conector Azure pode utilizar um Diretor de Serviço para autorizar contra o Microsoft Azure. Siga estes passos para criar um Diretor de Serviço para a Gestão da Paisagem SAP (LaMa).

1. Ir para https://portal.azure.com
1. Abra a lâmina do Diretório Ativo Azure
1. Clique em registos de aplicativos
1. Clique em Novo Registo
1. Introduza um nome e clique no Registo
1. Selecione a nova App e clique em Certificados & segredos no separador Definições
1. Crie um novo segredo de cliente, introduza uma descrição para uma nova chave, selecione quando o segredo deve expirar e clique em Guardar
1. Anota o Valor. É usado como a palavra-passe para o diretor de serviço
1. Escreva o ID da aplicação. É usado como o nome de utilizador do Diretor de Serviço

O Diretor de Serviço não tem permissões para aceder aos seus recursos Azure por padrão. Tem de dar ao Diretor de Serviço permissões para aceder a eles.

1. Ir para https://portal.azure.com
1. Abra a lâmina dos grupos de recursos
1. Selecione o grupo de recursos que pretende utilizar
1. Clique no controlo de acesso (IAM)
1. Clique em Adicionar atribuição de função
1. Selecione o contributo de função
1. Insira o nome da aplicação que criou acima
1. Clicar em Guardar
1. Repita o passo 3 a 8 para todos os grupos de recursos que pretende utilizar no SAP LaMa

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Use uma Identidade Gerida para ter acesso à AZure API

Para poder utilizar uma Identidade Gerida, o seu caso SAP LaMa tem de funcionar num VM Azure que tenha um sistema ou identidade atribuída ao utilizador. Para obter mais informações sobre identidades geridas, leia O que [](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) [são identidades geridas para recursos Azure?](../../../active-directory/managed-identities-azure-resources/overview.md)

A Identidade Gerida não tem permissões para aceder aos seus recursos Azure por padrão. Tens de lhe dar permissão para aceder a eles.

1. Ir para https://portal.azure.com
1. Abra a lâmina dos grupos de recursos
1. Selecione o grupo de recursos que pretende utilizar
1. Clique no controlo de acesso (IAM)
1. Clique em Adicionar -> Adicionar Atribuição de Função
1. Selecione o contributo de função
1. Selecione 'Máquina Virtual' para 'Atribuir acesso a'
1. Selecione a máquina virtual onde o seu caso SAP LaMa está em execução
1. Clicar em Guardar
1. Repita os passos para todos os grupos de recursos que pretende utilizar no SAP LaMa

Na configuração do conector SAP LaMa Azure, selecione 'Use Managed Identity' para permitir a utilização da Identidade Gerida. Se pretender utilizar uma identidade atribuída ao sistema, certifique-se de deixar o campo Nome de Utilizador vazio. Se pretender utilizar uma identidade atribuída ao utilizador, introduza o ID de identidade atribuído ao utilizador no campo Nome de Utilizador.

### <a name="create-a-new-connector-in-sap-lama"></a>Criar um novo conector no SAP LaMa

Abra o site da SAP LaMa e navegue para infraestrutura. Vá a guiar gestores de nuvem e clique em Adicionar. Selecione o Adaptador de Nuvem Microsoft Azure e clique em Seguinte. Introduza as seguintes informações:

* Etiqueta: Escolha um nome para a instância do conector
* Nome do utilizador: ID principal de aplicação de serviço ou identificação da identidade atribuída pelo utilizador da máquina virtual. Consulte [Utilizar um Sistema ou Identidade Atribuída ao Utilizador] para obter mais informações
* Palavra-passe: Chave/senha principal do serviço. Pode deixar este campo vazio se utilizar um sistema ou identidade atribuída pelo utilizador.
* URL: Manter o padrão `https://management.azure.com/`
* Intervalo de monitorização (segundos): Deve ser pelo menos 300
* Utilização Identidade Gerida: SAP LaMa pode utilizar um sistema ou identidade atribuída pelo utilizador para autenticar contra a API Azure. Consulte o capítulo [Utilize uma Identidade Gerida para ter acesso à API Azure](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) neste guia.
* ID de subscrição: ID de subscrição Azure
* ID do Inquilino Do Diretório Ativo Azure: ID do inquilino do Diretório Ativo
* Proxy host: Hostname do proxy se SAP LaMa precisar de um proxy para se conectar à internet
* Porta de procuração: porta TCP do representante
* Alterar tipo de armazenamento para economizar custos: Ative esta definição se o Adaptador Azure alterar o tipo de armazenamento dos Discos Geridos para economizar custos quando os discos não estiverem a ser utilizados. Para os discos de dados que são referenciados numa configuração de instância SAP, o adaptador alterará o tipo de disco para Standard Storage durante um caso de desprepare e volta ao tipo de armazenamento original durante uma preparação de exemplo. Se parar uma máquina virtual em SAP LaMa, o adaptador alterará o tipo de armazenamento de todos os discos anexos, incluindo o disco DE para o Armazenamento Padrão. Se iniciar uma máquina virtual no SAP LaMa, o adaptador mudará o tipo de armazenamento de volta para o tipo de armazenamento original.

Clique na Configuração de Teste para validar a sua entrada. Deverá ver

A ligação foi bem sucedida: A ligação à nuvem da Microsoft foi bem sucedida. 7 grupos de recursos encontrados (apenas 10 grupos solicitados)

na parte inferior do site.

## <a name="provision-a-new-adaptive-sap-system"></a>Disponibilização de um novo sistema SAP adaptativo

Pode implantar manualmente uma nova máquina virtual ou utilizar um dos modelos Azure no [repositório de arranque rápido](https://github.com/Azure/azure-quickstart-templates). Contém modelos para [OSCS SAP NetWeaver,](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs) [servidores de aplicações SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)e a [base de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Também pode usar estes modelos para providenciar novos anfitriões como parte de uma cópia/clone do sistema, etc.

Recomendamos a utilização de uma sub-rede separada para todas as máquinas virtuais que pretende gerir com o SAP LaMa e não utilize endereços IP dinâmicos para impedir que o endereço IP "roube" ao implementar novas máquinas virtuais e casos SAP não estão preparados.

> [!NOTE]
> Se possível, remova todas as extensões de máquinas virtuais, uma vez que podem causar longos períodos de funcionamento para separar os discos de uma máquina virtual.

Certifique-se de que \<hanasid> \<sapsid> existem adm, adm e seivas de grupo na máquina-alvo com o mesmo ID e gid ou utilização LDAP. Ativar e iniciar o servidor NFS nas máquinas virtuais que devem ser utilizadas para executar o SAP NetWeaver (A)SCS.

### <a name="manual-deployment"></a>Implantação manual

O SAP LaMa comunica com a máquina virtual utilizando o Agente Anfitrião SAP. Se utilizar manualmente as máquinas virtuais utilizando o modelo Azure Resource Manager a partir do repositório de arranque rápido, certifique-se de instalar o mais recente Agente anfitrião SAP e as Extensões Adaptáveis SAP. Para obter mais informações sobre os níveis de correção necessários para a Azure, consulte a nota [SAP 2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Implantação manual de uma máquina virtual Linux

Criar uma nova máquina virtual com um dos sistemas de funcionamento suportados listados na Nota [SAP 2343511]. Adicione configurações IP adicionais para as instâncias SAP. Cada instância precisa, pelo menos, de um endereço IP e deve ser instalada com um nome de hospedeiro virtual.

A instância ASCS SAP NetWeaver necessita de discos para /sapmnt/ \<SAPSID> , /usr/seiva/ \<SAPSID> /usr/seiva/trans, e /usr/seiva/adm. \<sapsid> Os servidores de aplicações SAP NetWeaver não precisam de discos adicionais. Tudo o que está relacionado com a instância SAP deve ser armazenado no ASCS e exportado através de NFS. Caso contrário, não é possível adicionar servidores de aplicações adicionais usando o SAP LaMa.

![SAP NetWeaver ASCS em Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Implantação manual para SAP HANA

Criar uma nova máquina virtual com um dos sistemas de funcionamento suportados para SAP HANA, conforme listado na Nota [SAP 2343511]. Adicione uma configuração IP adicional para SAP HANA e uma por inquilino HANA.

SAP HANA precisa de discos para /hana/shared, /hana/backup, /hana/data, e /hana/log

![SAP HANA em Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Implementação manual para a Base de Dados do Oráculo em Linux

Criar uma nova máquina virtual com um dos sistemas de funcionamento suportados para bases de dados oracle, conforme listado na Nota [SAP 2343511]. Adicione uma configuração IP adicional para a base de dados Oráculo.

A base de dados Oráculo precisa de discos para /oráculo, /home/oraod1, e /home/oráculo

![Diagrama que mostra uma base de dados do Oráculo no Linux e os discos de que precisa.](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Implementação manual para o Microsoft SQL Server

Crie uma nova máquina virtual com um dos sistemas de funcionamento suportados para o Microsoft SQL Server, tal como listado no SAP Note [2343511]. Adicione uma configuração IP adicional para a instância do SQL Server.

O servidor de base de dados SQL Server necessita de discos para os dados da base de dados e ficheiros de registo e discos para c:\usr\seiva.

![Base de dados Oráculo em Linux](media/lama/sap-lama-db-sql.png)

Certifique-se de instalar um controlador Microsoft ODBC suportado para o SQL Server numa máquina virtual que pretende utilizar para realojar um servidor de aplicação SAP NetWeaver para ou como um alvo de cópia/clone do sistema.

O SAP LaMa não pode realojar o próprio SQL Server de modo a que seja uma máquina virtual que pretende utilizar para realojar uma caixa de dados para ou como alvo de cópia/clone do sistema necessita de um Servidor SQL pré-instalado.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Implementar máquina virtual usando um modelo de azure

Descarregue os seguintes arquivos disponíveis do [SAP Software Marketplace](https://support.sap.com/swdc) para o sistema operativo das máquinas virtuais:

1. SAPCAR 7.21
1. SAP HOST AGENT 7.21
1. EXTENSÃO ADAPTÁVEL SAP 1.0 EXT

Descarregue também os seguintes componentes do [Microsoft Download Center](https://www.microsoft.com/download)

1. Microsoft Visual C++ 2010 Redistributable Package (x64) (apenas Windows)
1. Controlador ODBC da Microsoft para o SQL Server (apenas servidor SQL)

Os componentes são necessários para implantar o gabarito. A forma mais fácil de os disponibilizar ao modelo é carregá-los para uma conta de armazenamento Azure e criar uma Assinatura de Acesso Partilhado (SAS).

Os modelos têm os seguintes parâmetros:

* sapSystemId: O ID do sistema SAP. É utilizado para criar o layout do disco (por exemplo /usr/seiva/ \<sapsid> ).

* nome do computador: O nome do computador da nova máquina virtual. Este parâmetro também é usado pela SAP LaMa. Quando utiliza este modelo para forere uma nova máquina virtual como parte de uma cópia do sistema, o SAP LaMa aguarda até que o anfitrião com este nome de computador possa ser alcançado.

* osType: O tipo de sistema operativo que pretende implantar.

* dbtype: O tipo da base de dados. Este parâmetro é usado para determinar quantas configurações ip adicionais precisam de ser adicionadas e como o layout do disco deve ser.

* sapSystemSize: O tamanho do Sistema SAP que pretende implantar. É utilizado para determinar o tipo e o tamanho da caixa de identificação da máquina virtual.

* adminUsername: Nome de utilizador para a máquina virtual.

* adminPassword: Password para a máquina virtual. Também pode fornecer uma chave pública para o SSH.

* sshKeyData: Chave SSH pública para as máquinas virtuais. Suportado apenas para sistemas operativos Linux.

* subnetId: O ID da sub-rede que pretende utilizar.

* implementarEmptyTarget: Pode implementar um alvo vazio se quiser utilizar a máquina virtual como alvo para um caso de realocação ou similar. Neste caso, não são anexados discos adicionais ou configurações IP.

* sapcarLocation: A localização da aplicação sapcar que corresponde ao sistema operativo que implementa. o sapcar é utilizado para extrair os arquivos que fornece noutros parâmetros.

* sapHostAgentArchiveLocation: A localização do arquivo do Agente anfitrião SAP. O Agente anfitrião SAP é implantado como parte desta implementação do modelo.

* sapacExtLocation: A localização das Extensões Adaptáveis SAP. Sap Nota [2343511] lista o nível mínimo de remendos necessário para O Azure.

* vcLocalização DeRedist: A localização do tempo de execução do VC que é necessário para instalar as Extensões Adaptáveis SAP. Este parâmetro só é necessário para o Windows.

* odbcDriverLocation: A localização do controlador ODBC que pretende instalar. Apenas o controlador Microsoft ODBC para o SQL Server é suportado.

* sapadmPassword: A palavra-passe para o utilizador do sapadm.

* sapadmId: O ID do utilizador Linux do utilizador do sapadm. Não é necessário para o Windows.

* sapsysGid: O grupo Linux ID do grupo sapsys. Não é necessário para o Windows.

* _artifactsLocation: A base URI, onde estão localizados os artefactos exigidos por este modelo. Quando o modelo é implementado usando os scripts que o acompanham, uma localização privada na subscrição será usada e este valor será gerado automaticamente. Só é necessário se não implementar o modelo a partir do GitHub.

* _artifactsLocationSasToken: O sasToken necessário para aceder a _artifactsLocation. Quando o modelo é implantado usando os scripts que o acompanham, um sasToken será gerado automaticamente. Só é necessário se não implementar o modelo a partir do GitHub.

### <a name="sap-hana"></a>SAP HANA

Nos exemplos abaixo, assumimos que instala o SAP HANA com o sistema ID HN1 e o sistema SAP NetWeaver com o sistema ID AH1. Os nomes de anfitriões virtuais são hn1-db para a instância HANA, ah1-db para o inquilino HANA usado pelo sistema SAP NetWeaver, ah1-ascs para o SAP NetWeaver ASCS e ah1-di-0 para o primeiro servidor de aplicação SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Instale o SAP NetWeaver ASCS para SAP HANA utilizando discos geridos Azure

Antes de iniciar o GESTOR DE Provisionamento de Software SAP (SWPM), tem de montar o endereço IP do nome de anfitrião virtual do ASCS. A forma recomendada é utilizar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se de que volta a montar o endereço IP após um reboot.

![Logotipo linux.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Logotipo do Windows.][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Executar SWPM e usar *ah1-ascs* para o *nome de anfitrião ascs instance*.

![Logotipo linux.][Logo_Linux] Linux  
Adicione o seguinte parâmetro de perfil ao perfil do Agente anfitrião SAP, que está localizado em /usr/seiva/hostctrl/exe/host_profile. Para mais informações, consulte a Nota [SAP 2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Instalar SAP NetWeaver ASCS para SAP HANA em Azure NetAppFiles (ANF) BETA

> [!NOTE]
> Esta funcionalidade ainda não é GA. Para mais informações consulte a Nota [SAP 2815988] (apenas visível para pré-visualizar clientes).
Abra um incidente SAP no componente BC-VCM-LVM-HYPERV e solicite para se juntar ao adaptador de armazenamento LaMa para pré-visualização de Ficheiros Azure NetApp

A ANF fornece NFS para Azure. No contexto do SAP LaMa isto simplifica a criação dos casos dos Serviços Centrais ABAP (ASCS) e a subsequente instalação de servidores de aplicações. Anteriormente, a instância ASCS também tinha de funcionar como servidor NFS e o parâmetro acosprep/nfs_paths teve de ser adicionado à host_profile do Refém SAP.

#### <a name="anf-is-currently-available-in-these-regions"></a>A ANF está atualmente disponível nestas regiões:

Austrália Leste, Eua Centrais, Leste dos EUA, Leste dos EUA 2, Norte da Europa, Centro Sul dos EUA, Europa Ocidental e EUA Ocidental 2.

#### <a name="network-requirements"></a>Requisitos de rede

A ANF requer uma sub-rede delegada que deve fazer parte do mesmo VNET que os servidores SAP. Aqui está um exemplo para tal configuração.
Este ecrã mostra a criação do VNET e a primeira sub-rede:

![SAP LaMa cria rede virtual para Azure ANF ](media/lama/sap-lama-createvn-50.png)

O passo seguinte cria a sub-rede delegada para Microsoft.NetApp/volumes.

![SAP LaMa adiciona sub-rede delegada ](media/lama/sap-lama-addsubnet-50.png)

![Lista de sub-redes SAP LaMa ](media/lama/sap-lama-subnets.png)

Agora uma conta NetApp precisa de ser criada dentro do portal Azure:

![SAP LaMa cria conta NetApp ](media/lama/sap-lama-create-netappaccount-50.png)

![Conta SAP LaMa NetApp criada ](media/lama/sap-lama-netappaccount.png)

Dentro da conta NetApp, o pool de capacidade especifica o tamanho e o tipo de discos para cada piscina:

![SAP LaMa cria piscina de capacidade netApp ](media/lama/sap-lama-capacitypool-50.png)

![SAP LaMa NetApp capacity pool criado ](media/lama/sap-lama-capacitypool-list.png)

Os volumes NFS podem agora ser definidos. Uma vez que haverá volumes para vários sistemas numa só piscina, deve ser escolhido um esquema de nomeação autoexplicada. A adição do SID ajuda a agrupar volumes relacionados. Para o ASCS e para a instância AS são necessários os seguintes suportes: *\<SID\> /sapmnt/*, */usr/seiva/ \<SID\>* e */home/ \<sid\> adm*. Opcionalmente, */usr/seiva/trans* é necessário para o diretório de transporte central, que é pelo menos utilizado por todos os sistemas de uma paisagem.

> [!NOTE]
> Durante a fase BETA, o nome dos volumes deve ser único dentro da subscrição.

![SAP LaMa criar um volume 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa criar um volume 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa criar um volume 3 ](media/lama/sap-lama-createvolume3-80.png)

Estes passos também têm de ser repetidos para os outros volumes.

![Lista DE SAP LaMa de volumes criados ](media/lama/sap-lama-volumes.png)

Estes volumes devem agora ser montados nos sistemas onde será realizada a instalação inicial com o SAP SWPM.

Primeiro, os pontos de montagem precisam de ser criados. Neste caso, o SID é AN1, pelo que os seguintes comandos devem ser executados:

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

![SAP LaMa pontos de montagem nível OS ](media/lama/sap-lama-mounts.png)

Agora a instalação com SWPM deve ser realizada.

Os mesmos passos devem ser efetuados durante pelo menos uma instância as.

Após a instalação bem sucedida, o sistema deve ser descoberto dentro do SAP LaMa.

Os pontos de montagem devem ser assim para o ASCS e para o caso AS:

![Pontos de montagem SAP LaMa em LaMa ](media/lama/sap-lama-ascs.png) (Este é um exemplo. Os endereços IP e a trajetória de exportação são diferentes dos utilizados anteriormente)


#### <a name="install-sap-hana"></a>Instalar o SAP HANA

Se instalar o SAP HANA utilizando a ferramenta de comando hdblcm, utilize o nome de anfitrião para fornecer um nome de hospedeiro virtual. É necessário adicionar o endereço IP do nome de anfitrião virtual da base de dados a uma interface de rede. A forma recomendada é utilizar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se de que volta a montar o endereço IP após um reboot.

Adicione outro nome de anfitrião virtual e endereço IP para o nome que é usado pelos servidores de aplicações para ligar ao inquilino HANA.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Executar a instalação de instância de dados de SWPM na máquina virtual do servidor de aplicações, não na máquina virtual HANA. Utilize *ah1-db* para o anfitrião da *base de dados* na base de *dados de* diálogo para o sistema SAP .

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Instalar o Servidor de Aplicações SAP NetWeaver para SAP HANA

Antes de iniciar o GESTOR DE Provisionamento de Software SAP (SWPM), tem de montar o endereço IP do nome de anfitrião virtual do servidor de aplicações. A forma recomendada é utilizar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se de que volta a montar o endereço IP após um reboot.

![Logotipo linux.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Logotipo do Windows.][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Recomenda-se a utilização do parâmetro de perfil SAP NetWeaver dbs/hdb/hdb_use_ident para definir a identidade utilizada para encontrar a chave na loja de utilizadores HDB. Pode adicionar este parâmetro manualmente após a instalação de instância de base de dados com SWPM ou executar SWPM com

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Se o configurar manualmente, também precisa de criar novas entradas na loja de utilizadores HDB.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Utilize *ah1-di-0* para o *nome do anfitrião* da instância PAS em *identificação do servidor de aplicação primária*.

#### <a name="post-installation-steps-for-sap-hana"></a>Passos de pós-instalação para SAP HANA

Certifique-se de fazer o back up do SYSTEMDB e de todas as bases de dados de inquilinos antes de tentar fazer uma cópia do inquilino, mover o inquilino ou criar uma replicação do sistema.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

Nos exemplos abaixo, assumimos que instala o sistema SAP NetWeaver com o sistema ID AS1. Os nomes de anfitriões virtuais são as1-db para a instância SQL Server usada pelo sistema SAP NetWeaver, as1-ascs para o SAP NetWeaver ASCS e as1-di-0 para o primeiro servidor de aplicações SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Instale o SAP NetWeaver ASCS para o SQL Server

Antes de iniciar o GESTOR DE Provisionamento de Software SAP (SWPM), tem de montar o endereço IP do nome de anfitrião virtual do ASCS. A forma recomendada é utilizar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se de que volta a montar o endereço IP após um reboot.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Executar SWPM e usar *as1-ascs* para o *nome de anfitrião ascs instance*.

#### <a name="install-sql-server"></a>Instalar o SQL Server

É necessário adicionar o endereço IP do nome de anfitrião virtual da base de dados a uma interface de rede. A forma recomendada é utilizar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se de que volta a montar o endereço IP após um reboot.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Executar a instalação de instância de dados de SWPM na máquina virtual do servidor SQL. Utilize SAPINST_USE_HOSTNAME=*as1-db* para substituir o nome de anfitrião utilizado para ligar ao SQL Server. Se implementou a máquina virtual utilizando o modelo Azure Resource Manager, certifique-se de definir o diretório utilizado para os ficheiros de dados da base de dados para *C:\sql\data* e ficheiro de registo de base de dados para *C:\sql\log*.

Certifique-se de que o *utilizador NT AUTHORITY\SYSTEM* tem acesso ao SQL Server e tem a função de servidor *sysadmin*. Para mais informações, consulte a Nota SAP [1877727] e [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Instalar o servidor de aplicações SAP NetWeaver

Antes de iniciar o GESTOR DE Provisionamento de Software SAP (SWPM), tem de montar o endereço IP do nome de anfitrião virtual do servidor de aplicações. A forma recomendada é utilizar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se de que volta a montar o endereço IP após um reboot.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Utilize *o as1-di-0* para o *nome do anfitrião* da instância PAS em *identificação do servidor de aplicação primária*.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="errors-and-warnings-during-discover"></a>Erros e Avisos durante o Discover

* A permissão SELECT foi negada
  * [Microsoft] [Controlador de servidor ODBC SQL] [SQL Server] A permissão SELECT foi negada no objeto 'log_shipping_primary_databases', base de dados 'msdb', esquema 'dbo'. [SOAPFaultException]  
  A permissão SELECT foi negada no objeto 'log_shipping_primary_databases', base de dados 'msdb', esquema 'dbo'.
  * Solução  
    Certifique-se de que *o NT AUTHORITY\SYSTEM* pode aceder ao SQL Server. Ver Nota [SAP 2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Erros e avisos para validação de exemplo

* Foi levantada uma exceção na validação da loja de utilizadores hdb  
  * ver Log Viewer  
    com.sap.nw.lm.aci.monitor.api.api.validation.RuntimeValidationExcepção: Exceção no validador com ID 'RuntimeHDBConnectionValidator' (Validação: 'VALIDATION_HDB_USERSTORE'): Não conseguiu recuperar a hdbuserstore  
    A loja de utilizadores HANA não está no local correto
  * Solução  
    Certifique-se de que /usr/seiva/AH1/hdbclient/install/installation.ini está correto

### <a name="errors-and-warnings-during-a-system-copy"></a>Erros e avisos durante uma cópia do sistema

* Ocorreu um erro ao validar o passo de provisionamento do sistema
  * Causada por: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o nível=0 \; \; status=5 porta=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1--db -o level=0 \; \; status=5 port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p gancho -r
  * Solução  
    Faça cópias de segurança de todas as bases de dados no sistema HANA de origem

* Passo de cópia do sistema *Início* da instância da base de dados
  * A Operação agente anfitrião '000D3A282BC91EE8A1D76CF92E2944' falhou (OperaçãoExcepção. FaultCode: '127', Mensagem: 'Execução de comando falhou. : [Microsoft][ODBC SQL Server Driver][SQL Server]O utilizador não tem autorização para alterar a base de dados 'AS2', a base de dados não existe ou a base de dados não está num estado que permita controlos de acesso.»)
  * Solução  
    Certifique-se de que *o NT AUTHORITY\SYSTEM* pode aceder ao SQL Server. Ver Nota [SAP 2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Erros e avisos durante um clone do sistema

* Erro ocorreu ao tentar registar agente de instância no step *Forced Register and Start Instance Agent* do servidor de aplicações ou ASCS
  * Erro ocorreu ao tentar registar o agente de instância. (RemoteException: 'Failed to load instance data from profile ' \\ as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Não é possível aceder ao perfil ' \\ as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Não é possível aceder ao perfil de acesso ' as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0')
  * Solução  
   Certifique-se de que a partilha de sapmnt no ASCS/SCS tem acesso total para SAP_AS1_GlobalAdmin

* Erro no passo *Permitir proteção de startups para clone*
  * Não abriu o ficheiro ' \\ as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' Causa: Nenhum ficheiro ou diretório
  * Solução  
    A conta do computador do servidor de aplicações precisa de escrever acesso ao perfil

### <a name="errors-and-warnings-during-create-system-replication"></a>Erros e avisos durante a criação de replicação do sistema

* Exceção ao clicar na Replicação do Sistema Criar
  * Causada por: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o nível=0 \; \; status=5 porta=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1--db -o level=0 \; \; status=5 port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p gancho -r
  * Solução  
    Teste se sapacext pode ser executado como `<hanasid`>anúncio

* Erro quando a cópia completa não está ativada no Passo de Armazenamento
  * Ocorreu um erro ao reportar uma mensagem de atributo de contexto para o caminho IStorageCopyData.storageVolumeCopyList:1 e alvo de campoStorageSystemId
  * Solução  
    Ignore avisos em passo e tente de novo. Esta questão será corrigida num novo pacote de suporte/patch de SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Erros e avisos durante o realocar

* O caminho '/usr/seiva/AH1' não é permitido para reexportos nfs.
  * Verifique a nota [SAP 2628497] para obter mais detalhes.
  * Solução  
    Adicione as exportações asCS ao ascs HostAgent Profile. Ver Nota [SAP 2628497]

* Função não implementada ao deslocalizar o ASCS
  * Saída do comando: exportações: hospedeiro:/usr/seiva/AX1: Função não implementada
  * Solução  
    Certifique-se de que o serviço de servidor NFS está ativado na máquina virtual alvo de realocar

### <a name="errors-and-warnings-during-application-server-installation"></a>Erros e avisos durante a instalação do servidor de aplicações

* Erro de execução do passo SAPinst: getProfileDir
  * ERRO: (Último erro relatado pelo passo: Apanhado ESAPinstExcepção na chamada do módulo: Validador do passo '| NW_DI|ind|ind||ind|ind|ind|0|0| NW_GetSidFromProfiles|ind|ind||ind|ind|getSid|0| NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir" relatou um erro: Não existe nó \\ \as1-ascs\sapmnt\AS1\SYS\perfil. Inicie o SAPinst em modo interativo para resolver este problema)
  * Solução  
    Certifique-se de que o SWPM está a funcionar com um utilizador que tenha acesso ao perfil. Este utilizador pode ser configurado no assistente de instalação do servidor de aplicações

* Erro na execução do passo SAPinst: askUnicode
  * ERRO: (Último erro relatado pelo passo: Apanhado ESAPinstExcepção na chamada do módulo: Validador do passo '| NW_DI|ind|ind||ind|ind|ind|0|0| NW_GetSidFromProfiles|ind|ind||ind|ind|getSid|0| NW_getUnicode|ind|ind||ind|unicode|0|askUnicode' relatou um erro: Iniciar a SAPinst em modo interativo para resolver este problema)
  * Solução  
    Se utilizar um kernel SAP recente, o SWPM não pode determinar se o sistema é mais um sistema unicode utilizando o servidor de mensagens do ASCS. Consulte a Nota [SAP 2445033] para mais detalhes.  
    Esta questão será corrigida num novo pacote de suporte/patch de SAP LaMa.  
    Desata o parâmetro de perfil OS_UNICODE=uc no perfil padrão do seu sistema SAP para contornar este problema.

* Erro de execução do passo SAPinst: dCheckGivenServer
  * Erro na execução do passo SAPinst: dCheckGivenServer versão="1.0" ERROR: (Último erro reportado pelo passo: \<p> A instalação foi cancelada pelo utilizador. \</p>
  * Solução  
    Certifique-se de que o SWPM está a funcionar com um utilizador que tenha acesso ao perfil. Este utilizador pode ser configurado no assistente de instalação do servidor de aplicações

* Erro de execução do passo SAPinst: checkClient
  * Erro na execução do passo SAPinst: versão de controlo="1.0" ERROR: (Último erro reportado pelo passo: \<p> A instalação foi cancelada pelo utilizador. \</p>)
  * Solução  
    Certifique-se de que o controlador Microsoft ODBC para SQL Server está instalado na máquina virtual na qual pretende instalar o servidor de aplicações

* Erro de execução do passo SAPinst: copyScripts
  * Último erro relatado pelo passo: A chamada do sistema falhou. DETALHES: Erro 13 (0x0000000d) (Permissão negada) na execução da chamada de sistema 'fopenU' com parâmetro \\ (\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), linha (494) em ficheiro (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), traço de pilha:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl:::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase:::call  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction (iastring const& name, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream (args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl (CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcstrm2.cpp: 58: CSyFileStream2Impl:::CSyFileStream2Impl (const CSyPath & \\ \aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Solução  
    Certifique-se de que o SWPM está a funcionar com um utilizador que tenha acesso ao perfil. Este utilizador pode ser configurado no assistente de instalação do servidor de aplicações

* Erro na execução do passo SAPinst: askPasswords
  * Último erro relatado pelo passo: A chamada do sistema falhou. DETALHES: Erro 5 (0x00000005) (O acesso é negado.) na execução da chamada de sistema 'NetValidatePasswordPolicy' com parâmetro (...), linha (359) em ficheiro (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), traço de pilha:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl:::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase:::call  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl:::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase:::call  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction (iastring const& name, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy (args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::P asswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy (saponazure,****** )
  * Solução  
    Certifique-se de adicionar uma regra hospedeiro no meio *isolamento* para permitir a comunicação do VM ao controlador de domínio

## <a name="next-steps"></a>Passos seguintes
* [Manual de operações do SAP HANA no Azure][hana-ops-guide]
* [Azure Virtual Machines planejamento e implementação para SAP][planning-guide]
* [Implantação de máquinas virtuais Azure para SAP][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP][dbms-guide]
