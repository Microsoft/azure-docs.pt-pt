---
title: Como configurar o servidor SMT para SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Como configurar o servidor SMT para SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0d9e86e54a4c94db97b6c89b3ef8799855963020
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099772"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Configurar o servidor SMT para SUSE Linux
Instâncias grandes do SAP HANA não têm conectividade direta com a Internet. Não é um processo simples para registrar tal unidade com o provedor do sistema operacional e para baixar e aplicar atualizações. Uma solução para o SUSE Linux é configurar um servidor SMT em uma máquina virtual do Azure. Hospede a máquina virtual em uma rede virtual do Azure, que está conectada à instância grande do HANA. Com esse servidor SMT, a unidade de instância grande do HANA poderia registrar e baixar atualizações. 

Para obter mais documentação sobre o SUSE, consulte sua [ferramenta de gerenciamento de assinatura para SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Os pré-requisitos para a instalação de um servidor SMT que cumpre a tarefa para instâncias grandes do HANA são:

- Uma rede virtual do Azure que está conectada ao circuito de ExpressRoute de instância grande do HANA.
- Uma conta SUSE associada a uma organização. A organização deve ter uma assinatura SUSE válida.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Instalar o SMT Server em uma máquina virtual do Azure

Primeiro, entre no centro do [cliente do SUSE](https://scc.suse.com/).

Vá para**as credenciais da organização**da **organização** > . Nessa seção, você deve encontrar as credenciais necessárias para configurar o servidor SMT.

Em seguida, instale uma VM SUSE Linux na rede virtual do Azure. Para implantar a máquina virtual, use uma imagem da galeria do SLES 12 SP2 do Azure (selecione a imagem do BYOS SUSE). No processo de implantação, não defina um nome DNS e não use endereços IP estáticos.

![Captura de tela da implantação de máquina virtual para o servidor SMT](./media/hana-installation/image3_vm_deployment.png)

A máquina virtual implantada é menor e recebe o endereço IP interno na rede virtual do Azure de 10.34.1.4. O nome da máquina virtual é *smtserver*. Após a instalação, a conectividade com a unidade ou unidades de instância grande do HANA é verificada. Dependendo de como você organizou a resolução de nomes, talvez seja necessário configurar a resolução das unidades do SAP HANA em instâncias grandes no etc/hosts da máquina virtual do Azure. 

Adicione um disco à máquina virtual. Você usa esse disco para manter as atualizações, e o próprio disco de inicialização pode ser muito pequeno. Aqui, o disco foi montado em/SRV/www/htdocs, conforme mostrado na captura de tela a seguir. Um disco de 100 GB deve ser suficiente.

![Captura de tela da implantação de máquina virtual para o servidor SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Entre na unidade ou unidades de instância grande do HANA, mantenha/etc/hosts e verifique se você pode acessar a máquina virtual do Azure que deve executar o servidor SMT pela rede.

Após essa verificação, entre na máquina virtual do Azure que deve executar o servidor SMT. Se você estiver usando a reseqüência para entrar na máquina virtual, execute esta sequência de comandos em sua janela bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Reinicie o bash para ativar as configurações. Em seguida, inicie o YAST.

Conecte sua VM (smtserver) ao site do SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Depois que a máquina virtual estiver conectada ao site do SUSE, instale os pacotes SMT. Use o comando de reutilização a seguir para instalar os pacotes SMT.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Você também pode usar a ferramenta YAST para instalar os pacotes SMT. No YAST, vá para **manutenção de software**e procure por SMT. Selecione **SMT**, que alterna automaticamente para yast2-SMT.

![Captura de tela de SMT no YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Aceite a seleção para instalação no smtserver. Após a conclusão da instalação, vá para a configuração do servidor SMT. Insira as credenciais organizacionais do centro do cliente do SUSE que você recuperou anteriormente. Insira também o nome de host da máquina virtual do Azure como a URL do servidor SMT. Nesta demonstração, é https:\//smtserver.

![Captura de tela da configuração do servidor SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Agora, teste se a conexão com o centro do cliente SUSE funciona. Como você vê na captura de tela a seguir, neste caso de demonstração, ele funcionou.

![Captura de tela da conexão de teste para o centro do cliente SUSE](./media/hana-installation/image7_test_connect.png)

Depois que a instalação do SMT for iniciada, forneça uma senha de banco de dados. Como é uma nova instalação, você deve definir essa senha, conforme mostrado na captura de tela a seguir.

![Captura de tela da definição de senha para o banco de dados](./media/hana-installation/image8_define_db_passwd.PNG)

A próxima etapa é criar um certificado.

![Captura de tela da criação de um certificado para o servidor SMT](./media/hana-installation/image9_certificate_creation.PNG)

No final da configuração, pode levar alguns minutos para executar a verificação de sincronização. Após a instalação e a configuração do servidor SMT, você deve encontrar o repositório de diretório no ponto de montagem/SRV/www/htdocs/. Há também alguns subdiretórios no repositório. 

Reinicie o servidor SMT e seus serviços relacionados com estes comandos.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Baixar pacotes no servidor SMT

Depois que todos os serviços forem reiniciados, selecione os pacotes apropriados no gerenciamento de SMT usando o YAST. A seleção de pacote depende da imagem do sistema operacional do servidor do SAP HANA em instâncias grandes. A seleção de pacote não depende da versão SLES ou da máquina virtual que executa o servidor SMT. A captura de tela a seguir mostra um exemplo da exibição de seleção.

![Captura de tela da seleção de pacotes](./media/hana-installation/image10_select_packages.PNG)

Em seguida, inicie a cópia inicial dos pacotes SELECT para o servidor SMT que você configurou. Essa cópia é disparada no Shell usando o comando SMT-Mirror.

![Captura de tela do download de pacotes para o servidor SMT](./media/hana-installation/image11_download_packages.PNG)

Os pacotes devem ser copiados para os diretórios criados no ponto de montagem/SRV/www/htdocs. Esse processo pode levar uma hora ou mais, dependendo de quantos pacotes você selecionar. À medida que esse processo for concluído, vá para a configuração do cliente SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurar o cliente SMT em unidades de instância grande do HANA

O cliente ou os clientes nesse caso são as unidades de instância grande do HANA. A instalação do servidor SMT copiou o script clientSetup4SMT.sh para a máquina virtual do Azure. Copie esse script para a unidade de instância grande do HANA que você deseja conectar ao seu servidor do SMT. Inicie o script com a opção-h e forneça o nome do seu servidor SMT como um parâmetro. Neste exemplo, o nome é *smtserver*.

![Captura de tela da configuração do cliente SMT](./media/hana-installation/image12_configure_client.PNG)

É possível que a carga do certificado do servidor pelo cliente tenha êxito, mas o registro falha, conforme mostrado na captura de tela a seguir.

![Captura de tela de falha no registro do cliente](./media/hana-installation/image13_registration_failed.PNG)

Se o registro falhar, consulte o [documento de suporte do SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024)e execute as etapas descritas aqui.

> [!IMPORTANT] 
> Para o nome do servidor, forneça o nome da máquina virtual (nesse caso, *smtserver*), sem o nome de domínio totalmente qualificado. 

Depois de executar essas etapas, execute o seguinte comando na unidade de instância grande do HANA:

```
SUSEConnect –cleanup
```

> [!Note] 
> Aguarde alguns minutos após essa etapa. Se você executar o clientSetup4SMT.sh imediatamente, poderá receber um erro.

Se você encontrar um problema que precisa ser corrigido com base nas etapas do artigo do SUSE, reinicie o clientSetup4SMT.sh na unidade de instância grande do HANA. Agora ele deve ser concluído com êxito.

![Captura de tela de êxito no registro do cliente](./media/hana-installation/image14_finish_client_config.PNG)

Você configurou o cliente SMT da unidade de instância grande do HANA para se conectar ao servidor SMT instalado na máquina virtual do Azure. Agora você pode pegar ' zypper up ' ou ' zypper in ' para instalar atualizações do sistema operacional em instâncias grandes do HANA ou instalar pacotes adicionais. Você só pode obter atualizações que baixou antes no servidor SMT.

## <a name="next-steps"></a>Passos Seguintes
- [Instalação do Hana no HLI](hana-example-installation.md).











