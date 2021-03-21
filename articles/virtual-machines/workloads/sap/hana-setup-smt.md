---
title: Como configurar o servidor SMT para SAP HANA em Azure (Grandes Instâncias) | Microsoft Docs
description: Como configurar o servidor SMT para SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 990274f34dac0234c2c2d521ca6a260f999671bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674517"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Configurar o servidor SMT para SUSE Linux
Grandes casos de SAP HANA não têm conectividade direta com a internet. Não é um processo simples para registar tal unidade com o fornecedor do sistema operativo, e para descarregar e aplicar atualizações. Uma solução para o SUSE Linux é instalar um servidor SMT numa máquina virtual Azure. Hospedar a máquina virtual numa rede virtual Azure, que está ligada à Grande Instância HANA. Com um servidor SMT deste tipo, a unidade HANA Large Instance poderia registar e descarregar atualizações. 

Para obter mais documentação sobre suse, consulte a sua [Ferramenta de Gestão de Subscrição para SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Os pré-requisitos para a instalação de um servidor SMT que cumpra a tarefa para HANA Large Instances são:

- Uma rede virtual Azure que está ligada ao circuito HANA Large Instance ExpressRoute.
- Uma conta SUSE que está associada a uma organização. A organização deve ter uma subscrição SUSE válida.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Instale o servidor SMT numa máquina virtual Azure

Primeiro, inscreva-se no Centro de Apoio ao [Cliente da SUSE.](https://scc.suse.com/)

Vá às  >  **Credenciais de Organização.** Nessa secção, deverá encontrar as credenciais necessárias para configurar o servidor SMT.

Em seguida, instale um VM SUSE Linux na rede virtual Azure. Para implantar a máquina virtual, tire uma imagem de galeria SLES 12 SP2 de Azure (selecione imagem BYOS SUSE). No processo de implementação, não defina um nome DNS e não utilize endereços IP estáticos.

![Screenshot da implementação de máquina virtual para servidor SMT](./media/hana-installation/image3_vm_deployment.png)

A máquina virtual implantada é menor e obteve o endereço IP interno na rede virtual Azure de 10.34.1.4. O nome da máquina virtual é *smtserver*. Após a instalação, a conectividade com a unidade ou unidades de grande instância HANA é verificada. Dependendo da forma como organizou a resolução do nome, poderá ter de configurar a resolução das unidades HANA Large Instance em etc/anfitriões da máquina virtual Azure. 

Adicione um disco à máquina virtual. Usa este disco para conter as atualizações e o disco de arranque em si pode ser demasiado pequeno. Aqui, o disco foi montado para /srv/www/htdocs, como mostrado na imagem seguinte. Um disco de 100 GB deve ser suficiente.

![A screenshot mostra o disco adicionado na janela PuTTy.](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Inscreva-se na unidade ou unidades HANA Large Instance, mantenha /etc/anfitriões e verifique se pode chegar à máquina virtual Azure que supostamente executou o servidor SMT pela rede.

Após esta verificação, inscreva-se na máquina virtual Azure que deve executar o servidor SMT. Se estiver a usar massa para iniciar seduque na máquina virtual, execute esta sequência de comandos na sua janela de festa:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Reinicie a sua festa para ativar as definições. Então comece yast.

Ligue o seu VM (smtserver) ao site SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Depois de a máquina virtual ser ligada ao site SUSE, instale as embalagens de smt. Utilize o seguinte comando de massa para instalar os pacotes de smt.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Também pode utilizar a ferramenta YAST para instalar os pacotes de smt. Em YAST, vá à **Manutenção de Software** e procure smt. Selecione **smt**, que muda automaticamente para yast2-smt.

![Screenshot de SMT em YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Aceite a seleção para instalação no smtserver. Após a conclusão da instalação, aceda à configuração do servidor SMT. Insira as credenciais organizacionais do Centro de Clientes SUSE que recuperou anteriormente. Introduza também o seu nome de anfitrião de máquina virtual Azure como URL do SMT Server. Nesta demonstração, é https: \/ /smtserver.

![Screenshot da configuração do servidor SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Agora teste se a ligação ao Centro de Clientes SUSE funciona. Como pode ver na imagem que se segue, neste caso de demonstração, funcionou.

![Screenshot da ligação de teste ao Centro de Clientes SUSE](./media/hana-installation/image7_test_connect.png)

Após o início da configuração SMT, forneça uma senha de base de dados. Por ser uma nova instalação, deve definir essa palavra-passe como mostrado na imagem a seguir.

![Screenshot da definição da palavra-passe para base de dados](./media/hana-installation/image8_define_db_passwd.PNG)

O próximo passo é criar um certificado.

![Screenshot da criação de um certificado para servidor SMT](./media/hana-installation/image9_certificate_creation.PNG)

No final da configuração, pode levar alguns minutos para executar a verificação de sincronização. Após a instalação e configuração do servidor SMT, deverá encontrar o reeto de diretório sob o ponto de montagem /srv/www/htdocs/. Há também algumas subdiretivas sob repo. 

Reinicie o servidor SMT e os seus serviços relacionados com estes comandos.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Descarregue pacotes para o servidor SMT

Depois de todos os serviços serem reiniciados, selecione os pacotes apropriados na Gestão SMT utilizando o YAST. A seleção do pacote depende da imagem do sistema operativo do servidor HANA Large Instance. A seleção do pacote não depende da versão ou versão SLES da máquina virtual que executa o servidor SMT. A imagem que se segue mostra um exemplo do ecrã de seleção.

![Screenshot de selecionar pacotes](./media/hana-installation/image10_select_packages.PNG)

Em seguida, inicie a cópia inicial dos pacotes selecionados para o servidor SMT que configurar. Esta cópia é acionada na concha utilizando o espelho de comando.

![Screenshot de descarregar pacotes para servidor SMT](./media/hana-installation/image11_download_packages.PNG)

As embalagens devem ser copiadas para os diretórios criados sob o ponto de montagem /srv/www/htdocs. Este processo pode demorar uma hora ou mais, dependendo de quantos pacotes seleciona. À medida que este processo termina, mude para a configuração do cliente SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurar o cliente SMT em unidades de HANA Large Instance

O cliente ou clientes neste caso são as unidades HANA Large Instance. A configuração do servidor SMT copiou o script clientSetup4SMT.sh na máquina virtual Azure. Copie esse script para a unidade HANA Large Instance que pretende ligar ao seu servidor SMT. Inicie o script com a opção -h e dê o nome do seu servidor SMT como parâmetro. Neste exemplo, o nome é *smtserver*.

![Screenshot de configurar o cliente SMT](./media/hana-installation/image12_configure_client.PNG)

É possível que a carga do certificado do servidor pelo cliente tenha sucesso, mas o registo falha, como mostra a imagem seguinte.

![Screenshot da falha de registo do cliente](./media/hana-installation/image13_registration_failed.PNG)

Se o registo falhar, consulte o [documento de suporte SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024)e execute os passos aí descritos.

> [!IMPORTANT] 
> Para o nome do servidor, forneça o nome da máquina virtual (neste caso, *smtserver),* sem o nome de domínio totalmente qualificado. 

Depois de executar estes passos, executar o seguinte comando na unidade HANA Large Instance:

```
SUSEConnect –cleanup
```

> [!Note] 
> Espere alguns minutos depois daquele passo. Se correr clientSetup4SMT.sh imediatamente, pode ter um erro.

Se encontrar um problema que precisa de corrigir com base nos passos do artigo SUSE, reinicie clientSetup4SMT.sh na unidade HANA Large Instance. Agora deve terminar com sucesso.

![Screenshot do sucesso do registo do cliente](./media/hana-installation/image14_finish_client_config.PNG)

Configuraste o cliente SMT da unidade HANA Large Instance para ligar ao servidor SMT que instalou na máquina virtual Azure. Agora pode tomar 'zypper up' ou 'zypper in' para instalar atualizações do sistema operativo para HANA Large Instances ou instalar pacotes adicionais. Só é possível obter atualizações que descarregou antes no servidor SMT.

## <a name="next-steps"></a>Passos seguintes
- [Instalação HANA em HLI](hana-example-installation.md).











