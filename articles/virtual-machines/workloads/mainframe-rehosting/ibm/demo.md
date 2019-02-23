---
title: Definir se um aplicativo desenvolvedores controlado distribuição (ADCD) na IBM zD & T v1 | Documentos da Microsoft
description: Execute um ambiente de ambiente de teste (zD & T) e o IBM Z desenvolvimento em máquinas virtuais do Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: f366b9eee3104527fb9e974b51f339494b552e3b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739698"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Definir se um aplicativo desenvolvedores controlado distribuição (ADCD) na IBM zD & T v1

Pode executar um ambiente de ambiente de teste (zD & T) e o IBM Z desenvolvimento em máquinas virtuais do Azure (VMs). Neste ambiente emula a arquitetura de série do IBM Z. Ele pode alojar uma variedade de sistemas operacionais Z ou instalações (também denominadas Z instâncias ou pacotes), que estão disponíveis por meio de pacotes personalizados chamados as distribuições de controlado de desenvolvedores no IBM aplicativo (ADCDs).

Este artigo mostra-lhe como configurar uma instância ADCD num zD & o ambiente de T no Azure. ADCDs criar implementações de sistema operativo de série de Z completas para desenvolvimento e ambientes de teste que são executados em zD & T.

Como zD & T, ADCDs só estão disponíveis para clientes da IBM e parceiros e são exclusivamente para fins de testes e de desenvolvimento. Eles não são deve ser utilizada para ambientes de produção. Vários pacotes de instalação do IBM estão disponíveis para download por meio [partido de Passport](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) ou [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O [zD & ambiente T] [ ibm-install-z] configurou anteriormente no Azure. Este artigo pressupõe que está a utilizar a mesma imagem de VM do Ubuntu 16.04 criada anteriormente.

- Acesso ao suporte de dados ADCD através do IBM PartnerWorld ou partido do Passport.

- R [servidor de licenciamento](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Isto é necessário para executar a IBM zD & T. A forma como criá-la depende de como licenciamento de software da IBM:

  - **Servidor de licenciamento baseado em hardware** requer um dispositivo de hardware USB que contém os Tokens Rational necessárias ao acesso a todas as partes do software. Tem de obter isso da IBM.

  - **Servidor de licenciamento baseado em software** requer que defina um servidor centralizado para a gestão das chaves de licenciamento. Esse método é preferível e tem de configurar as chaves que recebe do IBM no servidor de gestão.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Transfira os pacotes de instalação da vantagem do Passport

Acesso ao suporte de dados ADCD é necessário. Os passos abaixo partem do princípio de que um clientes da IBM e pode utilizar o Passport partido. Podem utilizar a parceiros da IBM [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

> [!NOTE]
> Este artigo pressupõe que um PC Windows é utilizado para aceder ao portal do Azure e para transferir o suporte de dados do IBM. Se estiver a utilizar uma área de trabalho Mac ou Ubuntu, os comandos e o processo que permite obter o suporte de dados do IBM poderão ser ligeiramente diferentes.

1. Inicie sessão no [partido de Passport](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Selecione **Downloads de Software** e **acesso de suporte de dados**.

3. Selecione **número de oferta e o contrato do programa**e clique em **continuar**.

4. Introduza a descrição de parte ou o número de peça e clique em **Finder**.

5. Opcionalmente, clique na lista de ordem alfabética para exibir e ver theproduct por nome.

6. Selecione **todos os sistemas operativos** no **campo de sistema operativo**, e **todos os idiomas** no **campo idiomas**. Em seguida, clique em **ir**.

7. Clique em **seleção de arquivos individuais** para expandir a lista e exibir o suporte de dados individuais para transferir.

8. Verifique se o pacote (s) que pretende transferir, selecione **transferir**e, em seguida, transfira os ficheiros para o diretório desejado.

## <a name="upload-the-adcd-packages"></a>Carregar o pacote de ADCD (s)

Agora que tem o pacote (s), tem de os carregar para a VM no Azure.

1. No portal do Azure, iniciar um **ssh** sessão com a VM do Ubuntu que criou. Aceda à sua VM, selecione o **descrição geral** painel e, em seguida, selecione **Connect**.

2. Selecione o **SSH** separador e, em seguida, copie o comando ssh para a área de transferência.

3. Inicie sessão na sua VM com as suas credenciais e o [cliente SSH](/azure/virtual-machines/linux/use-remote-desktop) à escolha. Esta demonstração usa as extensões de Linux para o Windows 10, que adiciona um shell de bash de linha de comandos do Windows. PuTTY funciona igualmente bem.

4. Quando conectado, crie um diretório para carregar os pacotes da IBM. Lembre-se de Linux diferencia maiúsculas de minúsculas. Por exemplo, nesta demonstração supõe que os pacotes são carregados para:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Carregar ficheiros através de um cliente SSH como[WinSCP](https://winscp.net/eng/index.php). Uma vez que o SCP é uma parte do SSH, ele usa a porta 22, que é o que utiliza SSH. Se seu computador local não for Windows, pode digitar o [comando scp](http://man7.org/linux/man-pages/man1/scp.1.html) na sua sessão SSH.

6. Iniciar o carregamento para o diretório de VM do Azure que criou, que se torna o armazenamento de imagens para zD & T.

    > [!NOTE]
    > Certifique-se de que **ADCDTOOLS. XML** está incluído no carregamento para o **home/MyUserID/ZDT/adcd/nov2017** diretório. Precisará dele mais tarde.

7. Aguarde que os ficheiros carregar, que poderão demorar algum tempo consoante a ligação para o Azure.

8. Quando os carregamentos estiverem concluídos, navegue para o diretório de volumes e descomprimir todos os **gz** volumes:

    ```
        gunzip \*.gz
    ```
    
![](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Configurar o armazenamento de imagem

A próxima etapa é configurar zD & ionar para utilizar o pacote carregado (s). O processo de armazenamento de imagem dentro zD & T permite-lhe montar e usá-las. Ele pode utilizar o SSH ou FTP.

1. Iniciar o **zDTServer**. Para tal, tem de ser ao nível da raiz. Introduza os seguintes dois comandos por ordem:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Tenha em atenção a saída de URL pelo comando e utilize este URL para aceder ao servidor web. Que é semelhante a:
     > https://(your VM name or IP address):9443/ZDTMC/Index.HTML
     >
     > Lembre-se de que o acesso web utiliza a porta 9443. Utilize esta opção para iniciar sessão no servidor web. O ID de utilizador para ZD & T é **zdtadmin** e a palavra-passe é **palavra-passe**.

    ![](media/02-welcome.png)

3. Sobre o **início rápido** página, em **configurar**, selecione **imagem armazenamento**.

     ![](media/03-quickstart.png)

4. Sobre o **configurar o armazenamento de imagem** página, selecione **SSH File Transfer Protocol**.

5. Para **nome de anfitrião**, tipo **Localhost** e introduza o caminho do diretório para onde carregado as imagens. Por exemplo, /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Introduza o **ID de utilizador** e **palavra-passe** para a VM. Não utilize o ZD & T ID de utilizador e palavra-passe.

7. Testar a ligação para se certificar-se de que tem acesso e, em seguida, selecione **guardar** para guardar a configuração.

## <a name="configure-the-target-environments"></a>Configurar ambientes de destino

A próxima etapa é configurar o ambiente de destino da T & zD. Este ambiente alojado do emulados é onde executar suas imagens.

1. Sobre o **início rápido** página, em **configurar**, selecione **ambientes de destino**.

2. Sobre o **configurar ambientes de destino** página, selecione **adicionar destino**.

3. Selecione **Linux**. IBM suporta dois tipos de ambientes, Linux e Cloud(OpenStack), mas esta demonstração é executada no Linux.

4. Sobre o **adicionar ambiente de destino** página, para **nome do anfitrião**, introduza **localhost**. Manter **porta SSH** definida como **22**.

5. Na **etiqueta de ambiente de destino** , introduza uma etiqueta como **MyCICS.**

     ![](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Configurar ADCD e implementar

Depois de concluir os passos de configuração anteriores, tem de configurar zD & ionar para utilizar o ambiente de pacote (s) e de destino. Novamente, o processo de armazenamento de imagem no zD & T, que lhe permite montar e usá-las é utilizado. Ele pode utilizar o SSH ou FTP.

1. Sobre o **início rápido** página, em **configurar**, selecione **ADCD**. Um conjunto de instruções são apresentadas, informando que as etapas que precisam ser concluídas antes de um pacote ADCD pode ser montado. Isto explica por que o nome do diretório de destino a forma como fizemos anteriormente.

2. Partindo do princípio de que todas as imagens foram carregadas para os diretórios corretos, clique nas **imagem a partir de ADCD** link seja exibido na parte inferior direita (mostrada no passo 7 na captura de ecrã seguinte).

     ![](media/05-adcd.png)

## <a name="create-the-image"></a>Criar a imagem

Quando o passo de configuração anterior estiver concluído, o **criar uma imagem usando componentes de ADCD** é apresentada a página.

1. Selecione o volume (Novembro de 2017 neste caso) para apresentar os diferentes pacotes que estão nesse volume.

2. Para esta demonstração, selecione **ao cliente informações de controlo de sistema (CICS) - 5.3**.

3. Na **nome da imagem** , escreva um nome para a imagem como **MyCICS imagem**.

4. Selecione o **criar imagem** botão na parte inferior direita.

     ![](media/06-adcd.png)

5. Na janela que aparece, informando que a imagem foi implementada com êxito, escolha **implantar imagens**.

6. Sobre o **implantar uma imagem num ambiente de destino** página, selecione a imagem que criou na página anterior (**MyCICS imagem**) e o ambiente de destino que criou anteriormente (**MyCICS**).

7. No ecrã seguinte, forneça as credenciais para a VM (ou seja, não a ztadmin credencial).

8. No painel de propriedades, introduza o número de **processadores Central (CPs)**, a quantidade de **(GB) de memória do sistema**e o **diretório implementação** para a imagem em execução. Como essa é uma demonstração, mantenha as coisas pequenas.

9. Certifique-se a caixa está selecionada para **automaticamente o problema IPL comando para z/OS, depois de implementar**.

     ![](media/07-properties.png)

10. Selecione **concluída**.

11. Selecione **implementar imagem** partir a **implantar uma imagem num ambiente de destino** página.

Sua imagem agora pode implementar e está pronta para ser montadas por um emulador do terminal 3270.

> [!NOTE]
> Se receber um erro informando que não têm espaço suficiente em disco, tenha em atenção que a região requer 151 Gb.

Parabéns! Agora está executando um ambiente de mainframe IBM no Azure.

## <a name="learn-more"></a>Saiba mais

- [Migração de mainframe: mitos e fatos](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Resolução de problemas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Desmistificando do mainframe para a migração do Azure](https://azure.microsoft.com/en-us/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
