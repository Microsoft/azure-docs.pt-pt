---
title: Como configurar o servidor SMT para SAP HANA no Azure (Grandes Instâncias) [ Microsoft Docs
description: Como configurar o servidor SMT para SAP HANA no Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 699a8a69621642d07d3547c07bb20c0d32ca7686
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77616983"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Configurar o servidor SMT para SUSE Linux
Grandes instâncias de SAP HANA não têm conectividade direta com a internet. Não é um processo simples registar tal unidade com o fornecedor do sistema operativo, e descarregar e aplicar atualizações. Uma solução para o SUSE Linux é configurar um servidor SMT numa máquina virtual Azure. Hospedar a máquina virtual numa rede virtual Azure, que está ligada à Grande Instância HANA. Com um servidor SMT deste tipo, a unidade HANA Large Instance poderia registar e descarregar atualizações. 

Para obter mais documentação sobre sUSE, consulte a sua Ferramenta de Gestão de [Assinaturas para SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Os pré-requisitos para a instalação de um servidor SMT que cumpre a tarefa para as grandes instâncias hana são:

- Uma rede virtual Azure que está ligada ao circuito HANA Large Instance ExpressRoute.
- Uma conta SUSE que está associada a uma organização. A organização deve ter uma subscrição SUSE válida.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Instale o servidor SMT numa máquina virtual Azure

Primeiro, inscreva-se no Centro de [Clientes SUSE](https://scc.suse.com/).

Vá para**as credenciais**da **organização.** >  Nessa secção, deve encontrar as credenciais necessárias para configurar o servidor SMT.

Em seguida, instale um VM SUSE Linux na rede virtual Azure. Para implantar a máquina virtual, pegue uma imagem de galeria SLES 12 SP2 do Azure (selecione byos sUSE image). No processo de implementação, não defina um nome DNS e não utilize endereços IP estáticos.

![Screenshot da implementação de máquina virtual para servidor SMT](./media/hana-installation/image3_vm_deployment.png)

A máquina virtual implantada é menor, e obteve o endereço IP interno na rede virtual Azure de 10.34.1.4. O nome da máquina virtual é *smtserver*. Após a instalação, verifica-se a conectividade com a unidade ou unidades hana Large Instance. Dependendo da forma como organizou a resolução de nomes, poderá ser necessário configurar a resolução das unidades HANA Large Instance em etc/anfitriões da máquina virtual Azure. 

Adicione um disco à máquina virtual. Usa este disco para segurar as atualizações e o disco de arranque em si pode ser muito pequeno. Aqui, o disco foi montado para /srv/www/htdocs, como mostra a seguinte imagem. Um disco de 100 GB deve ser suficiente.

![Screenshot da implementação de máquina virtual para servidor SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Inscreva-se na unidade ou unidades HANA Large Instance, mantenha /etc/anfitriões e verifique se pode chegar à máquina virtual Azure que supostamente executa o servidor SMT sobre a rede.

Depois desta verificação, inscreva-se na máquina virtual Azure que deve executar o servidor SMT. Se estiver a usar massa para iniciar sessão na máquina virtual, execute esta sequência de comandos na sua janela de festa:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Reinicie a sua batida para ativar as definições. Então começa o YAST.

Ligue o seu VM (smtserver) ao site SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Depois de a máquina virtual estar ligada ao local suse, instale as embalagens de smt. Utilize o seguinte comando de massa para instalar as embalagens de smt.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Também pode utilizar a ferramenta YAST para instalar as embalagens de smt. No YAST, vá à **Manutenção de Software**e procure por Smt. Selecione **smt**, que muda automaticamente para yast2-smt.

![Screenshot de SMT em YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Aceite a seleção para instalação no smtserver. Depois de a instalação estar concluída, vá à configuração do servidor SMT. Introduza as credenciais organizacionais do Centro de Clientes SUSE que recuperou anteriormente. Introduza também o nome de anfitrião da máquina virtual Azure como URL do Servidor SMT. Nesta demonstração, é https:\//smtserver.

![Screenshot da configuração do servidor SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Agora teste se a ligação ao Centro de Clientes SUSE funciona. Como pode ver na seguinte imagem, neste caso de demonstração, resultou.

![Screenshot da ligação de teste ao Centro de Clientes SUSE](./media/hana-installation/image7_test_connect.png)

Depois do início da configuração do SMT, forneça uma senha de base de dados. Como é uma nova instalação, deve definir essa palavra-passe como mostrado na seguinte imagem.

![Screenshot de definição de palavra-passe para base de dados](./media/hana-installation/image8_define_db_passwd.PNG)

O próximo passo é criar um certificado.

![Screenshot de criar um certificado para servidor SMT](./media/hana-installation/image9_certificate_creation.PNG)

No final da configuração, pode levar alguns minutos para executar a verificação de sincronização. Após a instalação e configuração do servidor SMT, deverá encontrar o repo de diretório sob o ponto de montagem /srv/www/htdocs/. Há também alguns subdiretórios sob repo. 

Reinicie o servidor SMT e os seus serviços relacionados com estes comandos.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Descarregue os pacotes para o servidor SMT

Depois de todos os serviços serem reiniciados, selecione os pacotes apropriados na Gestão De SMT utilizando o YAST. A seleção do pacote depende da imagem do sistema operativo do servidor HANA Large Instance. A seleção do pacote não depende da versão ou versão do SLES que executa o servidor SMT. A imagem que se segue mostra um exemplo do ecrã de seleção.

![Screenshot de selecionar pacotes](./media/hana-installation/image10_select_packages.PNG)

Em seguida, inicie a cópia inicial dos pacotes selecionados para o servidor SMT que configura. Esta cópia é ativada na concha utilizando o espelho de comando.

![Screenshot de descarregar pacotes para servidor SMT](./media/hana-installation/image11_download_packages.PNG)

Os pacotes devem ser copiados para os diretórios criados sob o ponto de montagem /srv/www/htdocs. Este processo pode demorar uma hora ou mais, dependendo de quantos pacotes você seleciona. À medida que este processo termina, mude-se para a configuração do cliente SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurar o cliente SMT em unidades hana large instance

Os clientes ou clientes neste caso são as unidades HANA Large Instance. A configuração do servidor SMT copiou o guião clientSetup4SMT.sh para a máquina virtual Azure. Copie esse script para a unidade HANA Large Instance que pretende ligar ao seu servidor SMT. Inicie o script com a opção -h e dê o nome do seu servidor SMT como parâmetro. Neste exemplo, o nome é *smtserver*.

![Screenshot de configurar o cliente SMT](./media/hana-installation/image12_configure_client.PNG)

É possível que a carga do certificado do servidor pelo cliente tenha sucesso, mas o registo falha, como mostra a seguinte imagem.

![Screenshot da falha no registo do cliente](./media/hana-installation/image13_registration_failed.PNG)

Se o registo falhar, consulte o documento de [suporte SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024)e execute os passos descritos lá.

> [!IMPORTANT] 
> Para o nome do servidor, forneça o nome da máquina virtual (neste caso, *smtserver),* sem o nome de domínio totalmente qualificado. 

Depois de executar estes passos, execute o seguinte comando na unidade HANA Large Instance:

```
SUSEConnect –cleanup
```

> [!Note] 
> Espere alguns minutos depois do passo. Se correr clientSetup4SMT.sh imediatamente, pode ter um erro.

Se encontrar um problema que precisa de corrigir com base nos passos do artigo SUSE, reinicie clientSetup4SMT.sh na unidade HANA Large Instance. Agora deve terminar com sucesso.

![Screenshot do sucesso do registo do cliente](./media/hana-installation/image14_finish_client_config.PNG)

Configurou o cliente SMT da unidade HANA Large Instance para se ligar ao servidor SMT instalado na máquina virtual Azure. Agora pode pegar em 'zypper up' ou 'zypper in' para instalar atualizações do sistema operativo para as Grandes Instâncias HANA ou instalar pacotes adicionais. Só é possível obter atualizações que tenha descarregado antes no servidor SMT.

## <a name="next-steps"></a>Passos seguintes
- [Instalação HANA no HLI](hana-example-installation.md).











