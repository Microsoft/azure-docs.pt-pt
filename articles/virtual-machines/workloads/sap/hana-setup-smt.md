---
title: Como configurar o servidor SMT para SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Como configurar o servidor SMT para SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 690f41e941f2d1db8fc92d225a54d07570299222
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313140"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Configurar o servidor SMT para SUSE Linux
Instâncias grandes do HANA SAP não tem conectividade direta à internet. Não é um processo simples para registar tal uma unidade com o fornecedor do sistema operativo e para transferir e aplicar atualizações. Uma solução para o SUSE Linux é configurar um servidor SMT numa máquina virtual do Azure. Aloje a máquina virtual numa rede virtual do Azure, que está ligada à instância grande do HANA. Com esse um servidor SMT, a unidade de instância grande do HANA pode registar e transferir atualizações. 

Para obter mais documentação sobre SUSE, consulte seus [ferramenta de gestão de subscrição para o SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Pré-requisitos para instalar um servidor SMT que atenda a tarefa para instâncias grandes do HANA são:

- Uma rede virtual do Azure que está ligada ao circuito do ExpressRoute de instância grande do HANA.
- Uma conta SUSE que está associada uma organização. A organização deve ter uma subscrição válida do SUSE.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Instalar o servidor SMT numa máquina virtual do Azure

Primeiro, inicie sessão para o [atendimento ao consumidor do SUSE](https://scc.suse.com/).

Aceda a **organização** > **as credenciais da organização**. Nesta secção, encontrará as credenciais que são necessárias para configurar o servidor SMT.

Em seguida, instale uma VM do Linux SUSE na rede virtual do Azure. Para implementar a máquina virtual, pegar uma imagem da Galeria de SLES 12 SP2 do Azure (selecione BYOS SUSE imagem). O processo de implantação, não defina um nome DNS e não utilize endereços IP estáticos.

![Captura de ecrã da implementação da máquina virtual para o servidor SMT](./media/hana-installation/image3_vm_deployment.png)

A máquina virtual implementada é mais pequena e tem o endereço IP na rede virtual do Azure 10.34.1.4. O nome da máquina virtual é *smtserver*. Após a instalação, a conectividade com a unidade de instância grande do HANA ou unidades é verificada. Dependendo de como organizado de resolução de nomes, poderá ter de configurar a resolução das unidades de instância grande do HANA no etc/anfitriões de máquina virtual do Azure. 

Adicione um disco à máquina virtual. Utilize este disco para armazenar as atualizações e o disco de arranque em si pode ser demasiado pequeno. Aqui, o disco foi montado para /srv/www/htdocs, conforme mostrado na captura de ecrã seguinte. Um disco de 100 GB deve ser suficientes.

![Captura de ecrã da implementação da máquina virtual para o servidor SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Entrar para a unidade de instância grande do HANA ou unidades, manter /etc/hosts e verifique se pode chegar a máquina virtual do Azure que deve ser executado o servidor SMT através da rede.

Após esta verificação, inicie sessão na máquina virtual do Azure que deve executar o servidor SMT. Se estiver a utilizar o putty para iniciar sessão para a máquina virtual, execute esta sequência de comandos na janela do bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Reinicie o bash para ativar as definições. Em seguida, inicie YAST.

Ligue a sua VM (smtserver) para o site SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Depois da máquina virtual está ligada ao SUSE site, instale os pacotes de smt. Utilize o seguinte comando putty para instalar os pacotes de smt.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Também pode utilizar a ferramenta YAST para instalar os pacotes de smt. Na YAST, aceda a **manutenção do Software**e procure smt. Selecione **smt**, que muda automaticamente para yast2 smt.

![Captura de ecrã da SMT no YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Aceite a seleção para instalação no smtserver. Depois de concluída a instalação, vá para a configuração do servidor SMT. Introduza as credenciais organizacionais do Centro de SUSE obtido anteriormente. Também introduza o nome de anfitrião de máquina virtual do Azure como o URL do servidor SMT. Nesta demonstração, ele do https:\//smtserver.

![Configuração do servidor de captura de ecrã da SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Agora, teste se a ligação para o atendimento ao consumidor do SUSE funciona. Como pode ver na captura de ecrã seguinte, caso esta demonstração, ele funcionava.

![Captura de ecrã do teste de conexão com o atendimento ao consumidor do SUSE](./media/hana-installation/image7_test_connect.png)

Após a instalação SMT for iniciada, forneça uma palavra-passe da base de dados. Como é uma nova instalação, deve definir essa palavra-passe, conforme mostrado na captura de ecrã seguinte.

![Captura de ecrã da definição de palavra-passe para a base de dados](./media/hana-installation/image8_define_db_passwd.PNG)

A próxima etapa é criar um certificado.

![Captura de ecrã da criação de um certificado para o servidor SMT](./media/hana-installation/image9_certificate_creation.PNG)

No final da configuração, poderá demorar alguns minutos para executar a verificação de sincronização. Após a instalação e configuração do servidor SMT, deve encontrar o repositório de diretório numa montagem ponto /srv/www/htdocs /. Também existem alguns subdiretórios no repositório. 

Reinicie o servidor SMT e serviços relacionados com estes comandos.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Transferir pacotes do SMT server

Depois de todos os serviços são reiniciados, selecione os pacotes de apropriado na gestão de SMT utilizando YAST. A seleção de pacote depende da imagem do sistema operativo do servidor instância grande do HANA. A seleção de pacote não depende da versão SLES ou a versão da máquina virtual a executar o servidor SMT. Captura de ecrã seguinte mostra um exemplo do ecrã de seleção.

![Captura de ecrã da seleção de pacotes](./media/hana-installation/image10_select_packages.PNG)

Em seguida, inicie a cópia inicial dos pacotes selecionadas para o servidor SMT que configurou. Esta cópia é acionada no shell utilizando o comando smt-espelhamento.

![Captura de ecrã da transferência de pacotes para servidor SMT](./media/hana-installation/image11_download_packages.PNG)

Os pacotes devem ser copiados em diretórios criados sob a montagem ponto /srv/www/htdocs. Este processo pode demorar uma hora ou mais, dependendo de quantos pacotes que selecionar. Como esse processo terminar, mova para a configuração de cliente SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurar o cliente SMT em unidades de instância grande do HANA

O cliente ou clientes, neste caso, são as unidades de instância grande do HANA. A configuração de servidor SMT copiada clientSetup4SMT.sh o script na máquina virtual do Azure. Cópia do script sobre a unidade de instância grande do HANA pretende ligar ao seu servidor SMT. Inicie o script com a opção -h e atribua o nome do seu servidor SMT como um parâmetro. Neste exemplo, o nome é *smtserver*.

![Captura de ecrã de configuração de cliente a SMT](./media/hana-installation/image12_configure_client.PNG)

É possível que a carga do certificado do servidor pelo cliente for concluída com êxito, mas o registo falhar, conforme mostrado na captura de ecrã seguinte.

![Captura de ecrã de falha de registo de cliente](./media/hana-installation/image13_registration_failed.PNG)

Se o registo falhar, consulte [SUSE suporta documentos](https://www.suse.com/de-de/support/kb/doc/?id=7006024), e execute as etapas descritas aqui.

> [!IMPORTANT] 
> Para o nome do servidor, forneça o nome da máquina virtual (neste caso, *smtserver*), sem o nome de domínio completamente qualificado. 

Depois de executar estes passos, execute o seguinte comando na unidade instância grande do HANA:

```
SUSEConnect –cleanup
```

> [!Note] 
> Aguarde alguns minutos após esse passo. Se executar clientSetup4SMT.sh imediatamente, poderá receber um erro.

Se ocorrer um problema que precisa corrigir com base nos passos do artigo SUSE, reinicie clientSetup4SMT.sh na unidade instância grande do HANA. Agora, ele deve ser concluído com êxito.

![Captura de ecrã de êxito de registo do cliente](./media/hana-installation/image14_finish_client_config.PNG)

Configurar o cliente SMT da unidade instância grande do HANA para ligar ao servidor SMT instalado na máquina virtual do Azure. Agora pode demorar 'zypper se' ou "zypper no" para instalar atualizações do sistema operacional para instâncias grandes do HANA, ou instalar pacotes adicionais. Pode obter apenas as atualizações que transferiu anteriormente no servidor SMT.

## <a name="next-steps"></a>Passos Seguintes
- [Instalação de HANA HLI](hana-example-installation.md).











