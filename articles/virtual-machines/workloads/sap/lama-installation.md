---
title: Conector de SAP LaMa para o Azure | Documentos da Microsoft
description: Gerenciamento de sistemas SAP no Azure através de SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/17/2018
ms.author: sedusch
ms.openlocfilehash: f09f66e81ec4878aedebfee9be4c0c67b75c8ad6
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313605"
---
# <a name="sap-lama-connector-for-azure"></a>Conector de SAP LaMa para o Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Declaração de suporte geral: Sempre abra um incidente com o SAP no componente BC-VCM-LVM-Hyper-v se precisar de suporte para o SAP LaMa ou o conector do Azure.

SAP LaMa é utilizado por muitos clientes para operar e monitorar seu ambiente SAP. Desde SAP LaMa 3.0 SP05, ele é fornecido com um conector para o Azure por predefinição. Pode utilizar este conector para desalocar e iniciar máquinas virtuais, copiar e reposicionar discos geridos e eliminar os discos geridos. Com essas operações básicas, pode reposicionar, copiar, clonar e atualizar sistemas SAP com SAP LaMa.

Este guia descreve como configurar o conector do Azure para SAP LaMa, criar máquinas virtuais que podem ser utilizadas para instalar sistemas adaptativos do SAP e como configurá-las.

> [!NOTE]
> O conector só está disponível na edição de Enterprise LaMa do SAP

## <a name="resources"></a>Recursos

As seguintes notas de SAP estão relacionadas para o tópico de LaMa de SAP no Azure:

| Número de nota | Título |
| --- | --- |
| [2343511] |Conector do Microsoft Azure para a gestão de paisagem de SAP (LaMa) |
| [2350235] |SAP paisagem gestão 3.0 - Enterprise edition |

Leia também a [Portal para ajudar a SAP para o SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Observações gerais

* Certifique-se de habilitar *criação automática de ponto de montagem* no programa de configuração -> Definições -> motor  
  Se o SAP LaMa monta volumes usando as extensões de adaptável do SAP numa máquina virtual, o ponto de montagem tem de existir se esta definição não estiver ativada.

* Utilize a sub-rede separada e não utilize endereços IP dinâmicos para impedir que o IP endereços "roubar" quando implementar novas VMs e instâncias do SAP estão não preparadas  
  Se utilizar a atribuição de endereço IP dinâmico na sub-rede, que também é usada pelo SAP LaMa, preparar um sistema SAP com o SAP LaMa poderá falhar. Se um sistema SAP não preparado, os endereços IP não estão reservados e poderão obter alocados para outras máquinas virtuais.

* Se iniciar sessão anfitriões geridos, certifique-se de que não bloquear sistemas de ficheiros de desmontagem  
  Se faça logon para máquinas virtuais do Linux e altere o diretório de trabalho para um diretório num ponto de montagem, por exemplo, /usr/sap/AH1/ASCS00/exe, o volume não pode estar desmontado e uma realocá ou unprepare falhar.

## <a name="set-up-azure-connector-for-sap-lama"></a>Configurar o conector do Azure para SAP LaMa

O conector do Azure é enviado a partir do SAP LaMa 3.0 SP05. Recomendamos que instale sempre o pacote de suporte mais recente e o patch para o SAP LaMa 3.0. O conector do Azure utiliza um Principal de serviço para autorizar com o Microsoft Azure. Siga estes passos para criar um Principal de serviço para a gestão de paisagem de SAP (LaMa).

1. Ir para https://portal.azure.com
1. Abra o painel Azure Active Directory
1. Clique em registos de aplicações
1. Clique em Adicionar
1. Introduza um nome, selecione o tipo de aplicação "Aplicação/API da Web", introduza um URL de início de sessão (por exemplo, http:\//localhost) e clique em criar
1. O URL de início de sessão não é utilizado e pode ser qualquer URL válido
1. Selecione a nova aplicação e clique em chaves no separador Definições
1. Introduza uma descrição para uma nova chave, selecione "Nunca expira" e clique em Guardar
1. Anote o valor. É utilizado como a palavra-passe para o Principal de serviço
1. Anote o ID da aplicação. Ele é usado como o nome de utilizador do Principal de serviço

O Principal de serviço não tem permissões para aceder aos seus recursos do Azure por predefinição. Tem de conceder as permissões do Principal de serviço para aceder aos mesmos.

1. Ir para https://portal.azure.com
1. Abra o painel de grupos de recursos
1. Selecione o grupo de recursos que pretende utilizar
1. Clique em controle de acesso (IAM)
1. Clique em Adicionar atribuição de função
1. Selecione a função de contribuinte
1. Introduza o nome da aplicação que criou acima
1. Clicar em Guardar
1. Repita o passo 3 para 8 para todos os grupos de recursos que pretende utilizar no SAP LaMa

Abrir o site de SAP LaMa e navegue para a infraestrutura. Aceda ao separador gestores de nuvem e clique em Adicionar. Selecione a placa de Cloud do Microsoft Azure e clique em seguinte. Introduza as seguintes informações:

* Etiqueta: Escolha um nome para a instância do conector
* Nome de Utilizador: ID da Aplicação Principal de Serviço
* Palavra-passe: Chave de Principal de serviço/palavra-passe
* URL: Mantenha a predefinição https://management.azure.com/
* Intervalo de monitorização (segundos): Deve ser pelo menos 300
* ID da subscrição: ID de subscrição do Azure
* ID do inquilino do Azure Active Directory: ID do inquilino do Active Directory
* Anfitrião do proxy: Nome de anfitrião do proxy se SAP LaMa necessita um proxy para estabelecer ligação à internet
* Porta de proxy: Porta TCP do proxy

Clique em configuração de teste para validar a sua entrada. Deverá ver

Ligação estabelecida com êxito: Ligação para a nuvem da Microsoft foi concluída com êxito. grupos de recursos 7 encontrada (apenas 10 grupos solicitados)

na parte inferior do Web site.

## <a name="provision-a-new-adaptive-sap-system"></a>Aprovisionar um novo sistema SAP adaptável

Pode implementar uma nova máquina virtual ou utilize um dos modelos do Azure no manualmente os [repositório de início rápido](https://github.com/Azure/azure-quickstart-templates). Ele contém modelos para [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [servidores de aplicações SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)e o [base de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Também pode utilizar estes modelos para aprovisionar novos anfitriões como parte de um sistema cópia/clone etc.

Recomendamos que utilize uma sub-rede separada para todas as máquinas virtuais que pretende gerir com o SAP LaMa e não utilize endereços IP dinâmicos para impedir que o IP endereço "roubar" quando implementar novas máquinas virtuais e instâncias do SAP não preparadas.

> [!NOTE]
> Se possível, remova todas as extensões de máquina virtual como eles podem causar tempos de execução longos para desanexar discos a partir de uma máquina virtual.

Certifique-se de que o usuário \<hanasid > adm, \<sapsid > sapsys adm e o grupo existe no computador de destino com o mesmo ID e gid ou usam o LDAP. Ativar e iniciar o servidor NFS nas máquinas virtuais que deve ser utilizadas para executar o SAP NetWeaver (A) SCS.

### <a name="manual-deployment"></a>Implementação manual

SAP LaMa comunica com a máquina virtual com o agente de anfitrião do SAP. Se implementar manualmente as máquinas virtuais ou não a utilizar o modelo Azure Resource Manager do repositório de início rápido, certifique-se de instalar o agente de anfitrião mais recente do SAP e as extensões de adaptável SAP. Para obter mais informações sobre os níveis de patch necessária para o Azure, consulte a nota SAP [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Implementação manual de uma máquina de Virtual do Linux

Criar uma nova máquina virtual com um dos sistemas de operação suportada listados na nota SAP [2343511]. Adicione configurações de IP adicionais para as instâncias do SAP. Cada instância tem de, pelo menos, no endereço IP e tem de ser instalada com um nome de anfitrião virtual.

A instância do SAP NetWeaver ASCS necessita de discos para /sapmnt/\<SAPSID >, /usr/sap/\<SAPSID > e /usr/sap/trans/usr/sap/\<sapsid > adm. Os servidores de aplicações SAP NetWeaver não necessita de discos adicionais. Tudo relacionado à instância do SAP tem de ser armazenado em do ASCS e exportado por meio do NFS. Caso contrário, atualmente não é possível adicionar servidores de aplicativos usando SAP LaMa.

![SAP NetWeaver ASCS no Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Implementação manual para o SAP HANA

Criar uma nova máquina virtual com um dos sistemas de operação suportada para o SAP HANA, conforme listado na nota SAP [2343511]. Adicione uma configuração de IP adicional para o SAP HANA e um por inquilino do HANA.

SAP HANA necessita de discos para /hana/shared, /hana/backup, /hana/data e /hana/log

![SAP HANA no Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Implementação manual da base de dados do Oracle no Linux

Criar uma nova máquina virtual com um dos sistemas de operação suportada para bases de dados do Oracle, conforme listado na nota SAP [2343511]. Adicione uma configuração de IP adicional para a base de dados Oracle.

A base de dados do Oracle necessita de discos para /oracle, /home/oraod1 e /home/oracle

![Base de dados do Oracle no Linux](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Implementação manual para o Microsoft SQL Server

Criar uma nova máquina virtual com um dos sistemas de operação suportada para o Microsoft SQL Server, conforme listado na nota SAP [2343511]. Adicione uma configuração de IP adicional para a instância do SQL Server.

O servidor de base de dados do SQL Server necessita de discos para o banco de dados e ficheiros de registo e discos para c:\usr\sap.

![Base de dados do Oracle no Linux](media/lama/sap-lama-db-sql.png)

Certifique-se instalar um controlador Microsoft ODBC suportado para o SQL Server numa máquina virtual que pretende utilizar para a localização de um servidor de aplicações SAP NetWeaver para ou como um destino de clone/cópia do sistema.

SAP LaMa não é possível reposicionar próprio SQL Server, portanto, precisa de uma máquina virtual que pretende utilizar para a localização de uma instância de base de dados para ou como um destino de clone/cópia do sistema do SQL Server pré-instalado.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Implementar Máquina Virtual utilizando um modelo do Azure

Download seguinte mais recente disponível arquiva a partir da [mercado de Software SAP](https://support.sap.com/swdc) para o sistema operativo das máquinas virtuais:

1. SAPCAR 7.21
1. AGENTE DE ANFITRIÃO DO SAP 7.21
1. SAP EXTENSÃO ADAPTÁVEL 1.0 EXT

Também transferir os seguintes componentes do [Microsoft Download Center](https://www.microsoft.com/download)

1. Pacote redistribuível do Microsoft Visual C++ 2010 (x64) (apenas Windows)
1. Controlador Microsoft ODBC para o SQL Server (apenas para o SQL Server)

Os componentes são necessários para implementar o modelo. A maneira mais fácil para que fiquem disponíveis para o modelo é carregá-los para uma conta de armazenamento do Azure e criar uma assinatura de acesso partilhado (SAS).

Os modelos têm os seguintes parâmetros:

* sapSystemId: O ID de sistema SAP. Ele é usado para criar o layout de disco (por exemplo/usr/sap/\<sapsid >).

* computerName: O nome do computador da máquina virtual nova. Este parâmetro, também é usado pelo SAP LaMa. Quando utiliza este modelo para Aprovisionar uma nova máquina virtual como parte de uma cópia do sistema, o SAP LaMa aguarda até que o anfitrião com este nome de computador pode ser contatado.

* osType: O tipo do sistema operativo que pretende implementar.

* dbtype: O tipo de base de dados. Este parâmetro é utilizado para determinar quantas configurações de IP adicionais é necessário adicionar e como o esquema do disco deve ser semelhante.

* sapSystemSize: O tamanho do sistema SAP que pretende implementar. É utilizado para determinar o tipo de instância de máquina virtual e o tamanho.

* adminUsername: Nome de utilizador para a máquina virtual.

* adminPassword: Palavra-passe para a máquina virtual. Também pode fornecer uma chave pública de SSH.

* sshKeyData: Chave pública de SSH para as máquinas virtuais. Só é suportada para sistemas operativos Linux.

* subnetId: O ID da sub-rede que pretende utilizar.

* deployEmptyTarget: Pode implementar um destino vazio se pretender utilizar a máquina virtual como um destino para realocá uma instância ou semelhante. Neste caso, não existem discos adicionais ou configurações de IP são anexadas.

* sapcarLocation: A localização para a aplicação de sapcar que corresponde ao sistema operativo que implementar. sapcar é utilizado para extrair os arquivos mortos que fornecer nos outros parâmetros.

* sapHostAgentArchiveLocation: A localização do arquivo morto do agente de anfitrião do SAP. Agente de anfitrião do SAP é implementada como parte desta implementação do modelo.

* sapacExtLocation: A localização das extensões adaptável SAP. A nota SAP [2343511] indica o nível mínimo de correção necessário para o Azure.

* vcRedistLocation: A localização do tempo de execução do VC que é necessário para instalar as extensões de adaptável SAP. Este parâmetro só é necessário para Windows.

* odbcDriverLocation: A localização do controlador ODBC que pretende instalar. Apenas a Microsoft ODBC driver para SQL Server é suportada.

* sapadmPassword: A palavra-passe para o utilizador sapadm.

* sapadmId: O ID de utilizador do Linux do utilizador sapadm. Não é necessário para Windows.

* sapsysGid: O ID de grupo do Linux do grupo sapsys. Não é necessário para Windows.

* _artifactsLocation: O URI de base, onde se encontram os artefatos necessários por este modelo. Quando o modelo é implementado utilizando os scripts que acompanha este artigo, vai ser utilizada numa localização privada na subscrição e este valor será gerado automaticamente. Apenas necessário se o utilizador não implementa o modelo a partir do GitHub.

* _artifactsLocationSasToken: O sasToken necessária para aceder a artifactslocation. Quando o modelo é implementado utilizando os scripts que acompanha este artigo, um sasToken será gerada automaticamente. Apenas necessário se o utilizador não implementa o modelo a partir do GitHub.

### <a name="sap-hana"></a>SAP HANA

Os exemplos abaixo, partimos do pressuposto que instalar o SAP HANA com o sistema HN1 de ID e o sistema de SAP NetWeaver com sistema AH1 de ID. Os nomes de anfitrião virtual são hn1-db para a instância do HANA, ah1-db para o inquilino HANA utilizado pelo sistema SAP NetWeaver, ah1-ascs para o SAP NetWeaver ASCS e ah1-di-0 para o servidor de aplicações SAP NetWeaver primeiro.

#### <a name="install-sap-netweaver-ascs-for-sap-hana"></a>Instalar o SAP NetWeaver ASCS para o SAP HANA

Antes de iniciar o Gestor de aprovisionamento de Software para SAP (SWPM), terá de montar o endereço IP do nome de anfitrião virtual do ASCS. A forma recomendada é usar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se remontar o endereço IP após um reinício.

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

Executar SWPM e utilizar *ah1 ascs* para o *nome de anfitrião da instância de ASCS*.

![Linux][Logo_Linux] Linux  
Adicione o seguinte parâmetro de perfil para o perfil de agente de anfitrião do SAP, o que está localizado em /usr/sap/hostctrl/exe/host_profile. Para obter mais informações, consulte a nota SAP [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-hana"></a>Instalar o SAP HANA

Se instalar o SAP HANA com o hdblcm da ferramenta de linha de comandos, utilize o parâmetro – nome do anfitrião para fornecer um nome de anfitrião virtual. Tem de adicionar o endereço IP do nome do anfitrião virtual da base de dados para uma interface de rede. A forma recomendada é usar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se remontar o endereço IP após um reinício.

Adicione outro nome de anfitrião virtual e de endereços IP para o nome que é utilizado pelos servidores de aplicação para ligar ao inquilino do HANA.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Execute a instalação de instância de base de dados do SWPM na máquina de virtual de servidor de aplicativo, e não da máquina virtual do HANA. Uso *ah1-db* para o *anfitrião de base de dados* na caixa de diálogo *base de dados para o sistema SAP*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Instalar o servidor de aplicações SAP NetWeaver para o SAP HANA

Antes de iniciar o Gestor de aprovisionamento de Software para SAP (SWPM), terá de montar o endereço IP do nome de anfitrião virtual do servidor de aplicativos. A forma recomendada é usar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se remontar o endereço IP após um reinício.

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

Recomenda-se para utilizar o SAP NetWeaver perfil parâmetro bds/hdb/hdb_use_ident para definir a identidade que é utilizada para localizar a chave no HDB userstore. Pode adicionar esse parâmetro manualmente após a instalação de instância de base de dados com SWPM ou executar SWPM com

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Se defini-lo manualmente, terá também de criar novas entradas de userstore HDB.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Uso *ah1-di-0* para o *nome de anfitrião da instância de PAS* na caixa de diálogo *instância de servidor principal do aplicativo*.

#### <a name="post-installation-steps-for-sap-hana"></a>Passos de pós-instalação para o SAP HANA

Certifique-se criar cópias de segurança a SYSTEMDB e todas as bases de dados do inquilino antes de tentar fazer uma cópia do inquilino, movimentação de inquilinos ou crie uma replicação do sistema.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

Nos exemplos abaixo, partimos do princípio de que instala o sistema SAP NetWeaver com sistema AS1 de ID. Os nomes de anfitrião virtual são as1-db para a instância de SQL Server utilizada pelo sistema SAP NetWeaver, as1-ascs para o SAP NetWeaver ASCS e as1-di-0 para o servidor de aplicações SAP NetWeaver primeiro.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Instalar o SAP NetWeaver ASCS para o SQL Server

Antes de iniciar o Gestor de aprovisionamento de Software para SAP (SWPM), terá de montar o endereço IP do nome de anfitrião virtual do ASCS. A forma recomendada é usar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se remontar o endereço IP após um reinício.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Executar SWPM e utilizar *as1 ascs* para o *nome de anfitrião da instância de ASCS*.

#### <a name="install-sql-server"></a>Instalar o SQL Server

Tem de adicionar o endereço IP do nome do anfitrião virtual da base de dados para uma interface de rede. A forma recomendada é usar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se remontar o endereço IP após um reinício.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Execute a instalação de instância de base de dados do SWPM na máquina de virtual do SQL server. Utilizar SAPINST_USE_HOSTNAME =*as1-db* para substituir o nome de anfitrião utilizado para ligar ao SQL Server. Se implementou a máquina virtual com o modelo Azure Resource Manager, certifique-se de definir o diretório utilizado para os ficheiros de dados de base de dados para *C:\sql\data* e o ficheiro de registo de base de dados para *C:\sql\log*.

Certifique-se de que o usuário *NT AUTHORITY\SYSTEM* tem acesso ao SQL Server e tem a função de servidor *sysadmin*. Para obter mais informações, consulte a nota SAP [1877727] e [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Instalar o servidor de aplicações SAP NetWeaver

Antes de iniciar o Gestor de aprovisionamento de Software para SAP (SWPM), terá de montar o endereço IP do nome de anfitrião virtual do servidor de aplicativos. A forma recomendada é usar sapacext. Se montar o endereço IP utilizando sapacext, certifique-se remontar o endereço IP após um reinício.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Uso *as1-di-0* para o *nome de anfitrião da instância de PAS* na caixa de diálogo *instância de servidor principal do aplicativo*.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="errors-and-warnings-during-discover"></a>Erros e avisos durante a deteção

* A permissão de SELEÇÃO foi negada
  * [Microsoft] [ODBC SQL Server Driver] [SQL Server] A permissão de SELEÇÃO foi negada no objeto 'log_shipping_primary_databases', base de dados 'msdb', esquema 'dbo'. [SOAPFaultException]  
  A permissão de SELEÇÃO foi negada no objeto 'log_shipping_primary_databases', base de dados 'msdb', esquema 'dbo'.
  * Solução  
    Certifique-se de que *NT AUTHORITY\SYSTEM* pode aceder ao SQL Server. Consulte a nota SAP [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Por exemplo, validação de avisos e erros

* Ocorreu uma exceção na validação de HDB userstore  
  * consulte o Log Viewer  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: Exceção na validação com o ID 'RuntimeHDBConnectionValidator' (validação: 'VALIDATION_HDB_USERSTORE'): Não foi possível obter o hdbuserstore  
    HANA userstore não está na localização correta
  * Solução  
    Certifique-se de que /usr/sap/AH1/hdbclient/install/installation.ini está correto

### <a name="errors-and-warnings-during-a-system-copy"></a>Erros e avisos durante uma cópia do sistema

* Ocorreu um erro ao validar o sistema de provisionamento passo
  * Causado por: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException chamar ' / usr/sap/hostctrl/exe/sapacext - um nível de -o de hn1-db de 50 - h -f -m HN1 ShowHanaBackups = 0\;Estado = 5\;porta = 35013 pf = / usr/sap/hostctrl / exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook - r' | /usr/SAP/hostctrl/exe/sapacext - ao nível do ShowHanaBackups -m HN1 -f 50 - h hn1-db - s = 0\;Estado = 5\;porta = 35013 pf = / usr/sap/hostctrl/exe/host_profile - R -T dev_lvminfo -u SYSTEM -p hook - r
  * Solução  
    Efetuar cópia de segurança de todas as bases de dados na origem de sistema HANA

* Passo de cópia de sistema *iniciar* da instância de base de dados
  * Operação do agente de anfitrião "000D3A282BC91EE8A1D76CF1F92E2944" falhou (OperationException. FaultCode: "127', mensagem: "Falha na execução de comando. : O utilizador [Microsoft] [controlador ODBC do SQL Server] [SQL Server] não tem permissão para alterar a base de dados 'AS2', a base de dados não existe ou a base de dados não está num Estado que permita verificação de acesso. ")
  * Solução  
    Certifique-se de que *NT AUTHORITY\SYSTEM* pode aceder ao SQL Server. Consulte a nota SAP [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Erros e avisos durante um Clone de sistema

* Ocorreu um erro ao tentar registar o agente de instância na etapa *forçado registe-se e iniciar o agente de instância* do servidor de aplicativos ou ASCS
  * Ocorreu um erro ao tentar registar o agente de instância. (RemoteException: "Falha ao carregar dados de instância do perfil"\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0 ":  Não é possível aceder ao perfil '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0 ": Não existe esse ficheiro ou diretório. ")
  * Solução  
   Certifique-se de que a partilha de /sapmnt no ASCS/SCS tem acesso total para SAP_AS1_GlobalAdmin

* Erro no passo *ativar proteção de arranque para clonagem*
  * Falha ao abrir o ficheiro '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0 "causa: Não existe esse ficheiro ou diretório
  * Solução  
    A conta de computador do servidor de aplicativos precisa de acesso de escrita para o perfil

### <a name="errors-and-warnings-during-create-system-replication"></a>Erros e avisos durante a criação de replicação de sistema

* Exceção ao clicar em criar replicação do sistema
  * Causado por: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException chamar ' / usr/sap/hostctrl/exe/sapacext - um nível de -o de hn1-db de 50 - h -f -m HN1 ShowHanaBackups = 0\;Estado = 5\;porta = 35013 pf = / usr/sap/hostctrl / exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook - r' | /usr/SAP/hostctrl/exe/sapacext - ao nível do ShowHanaBackups -m HN1 -f 50 - h hn1-db - s = 0\;Estado = 5\;porta = 35013 pf = / usr/sap/hostctrl/exe/host_profile - R -T dev_lvminfo -u SYSTEM -p hook - r
  * Solução  
    Testar se sapacext pode ser executado como `<hanasid`> adm

* Ocorreu um erro ao copiar completo não está ativado no passo de armazenamento
  * Ocorreu um erro quando uma mensagem de atributo de contexto para o caminho IStorageCopyData.storageVolumeCopyList:1 e targetStorageSystemId de campo de geração de relatórios
  * Solução  
    Ignorar avisos de passo e tente novamente. Este problema será corrigido num novo suporte de pacote/patch de LaMa de SAP.

### <a name="errors-and-warnings-during-relocate"></a>Erros e avisos durante Realocá

* Caminho '/ usr/sap/AH1' não é permitida para nfs reexports.
  * Consulte a nota SAP [2628497] para obter detalhes.
  * Solução  
    Adicione que ASCS exporta para ASCS HostAgent perfil. Consulte a nota SAP [2628497]

* Não implementada quando a alteração da localização ASCS de função
  * Saída do comando: exportfs: anfitrião: / usr/sap/AX1: Função não implementada
  * Solução  
    Certifique-se de que o serviço do servidor NFS está ativado na máquina de virtual de destino realocá

### <a name="errors-and-warnings-during-application-server-installation"></a>Erros e avisos durante a instalação do servidor de aplicação

* Erro ao executar o passo de SAPinst: getProfileDir
  * ERRO: (Último erro comunicado pelo passo: ESAPinstException capturado na chamada de módulo: A validação do passo "| NW_DI | ind | ind | ind | ind | 0 | 0 | NW_GetSidFromProfiles | ind | ind | ind | ind | getSid | 0 | NW_readProfileDir | ind | ind | ind | ind | readProfile | 0 | getProfileDir "comunicou um erro: Nó \\\as1-ascs\sapmnt\AS1\SYS\profile não existe. Iniciar SAPinst no modo interativo para resolver esse problema)
  * Solução  
    Certifique-se de que o SWPM está em execução com um utilizador que tenha acesso ao perfil. Este utilizador pode ser configurado no Assistente de instalação do servidor de aplicativos

* Erro ao executar o passo de SAPinst: askUnicode
  * ERRO: (Último erro comunicado pelo passo: ESAPinstException capturado na chamada de módulo: A validação do passo "| NW_DI | ind | ind | ind | ind | 0 | 0 | NW_GetSidFromProfiles | ind | ind | ind | ind | getSid | 0 | NW_getUnicode | ind | ind | ind | ind | unicode | 0 | askUnicode "comunicou um erro: Iniciar SAPinst no modo interativo para resolver esse problema)
  * Solução  
    Se utilizar um kernel mais recente do SAP, SWPM não consegue determinar se o sistema é um sistema de unicode deixa de poder utilizar o servidor de mensagens do ASCS. Consulte a nota SAP [2445033] para obter mais detalhes.  
    Este problema será corrigido num novo suporte de pacote/patch de LaMa de SAP.  
    Defina o parâmetro de perfil OS_UNICODE = uc no perfil predefinido do seu sistema SAP para contornar este problema.

* Erro ao executar o passo de SAPinst: dCheckGivenServer
  * Erro ao executar o passo de SAPinst: dCheckGivenServer "versão ="1.0"erro: (Último erro comunicado pelo passo: \<p > instalação foi cancelada pelo utilizador. \</p>
  * Solução  
    Certifique-se de que o SWPM está em execução com um utilizador que tenha acesso ao perfil. Este utilizador pode ser configurado no Assistente de instalação do servidor de aplicativos

* Erro ao executar o passo de SAPinst: checkClient
  * Erro ao executar o passo de SAPinst: checkClient "versão ="1.0"erro: (Último erro comunicado pelo passo: \<p > instalação foi cancelada pelo utilizador. \</p>)
  * Solução  
    Certifique-se de que o controlador Microsoft ODBC para SQL Server está instalado na máquina virtual no qual pretende instalar o servidor de aplicações

* Erro ao executar o passo de SAPinst: copyScripts
  * Último erro comunicado pelo passo: Falha na chamada de sistema. DETALHES: Erro 13 (0x0000000d) (permissão negada), na execução do sistema, chamar fopenU com parâmetro (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), linha (494) arquivo (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib / filesystem/syxxcfstrm2.cpp), rastreio de pilha:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction (iastring const & nome, args_t const & args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream (args_t const & _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl (const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Solução  
    Certifique-se de que o SWPM está em execução com um utilizador que tenha acesso ao perfil. Este utilizador pode ser configurado no Assistente de instalação do servidor de aplicativos

* Erro ao executar o passo de SAPinst: askPasswords
  * Último erro comunicado pelo passo: Falha na chamada de sistema. DETALHES: Erro de 5 (0x00000005) (o acesso é negado.) na execução da chamada de sistema 'NetValidatePasswordPolicy' com o parâmetro (.....), linha (359) arquivo (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), o rastreio de pilha:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction (iastring const & nome, args_t const & args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy (args_t const & _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: CSyAccountMgtImpl::validatePasswordPolicy(saponazure,***) ISyAccountMgt::PasswordValidationMessage const)
  * Solução  
    Certifique-se de adicionar uma regra de anfitrião na etapa *isolamento* para permitir a comunicação da VM para o controlador de domínio

## <a name="next-steps"></a>Passos Seguintes
* [SAP HANA no guia de operações do Azure][hana-ops-guide]
* [Máquinas de virtuais de planeamento e implementação de SAP do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
