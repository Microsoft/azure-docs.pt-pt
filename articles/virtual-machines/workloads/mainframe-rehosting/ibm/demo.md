---
title: Criar uma distribuição controlada por desenvolvedores de aplicações (ADCD) na IBM zD&T v1 / Microsoft Docs
description: Gere um ambiente IBM Z Development and Test Environment (zD&T) em Máquinas Virtuais Azure (VMs).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 66f80c79219090c27da37dfc1d9149df5604961f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68841382"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Criar uma distribuição controlada por desenvolvedores de aplicações (ADCD) em IBM zD&T v1

Você pode executar um ambiente IBM Z Desenvolvimento e Teste Ambiente (zD&T) em Máquinas Virtuais Azure (VMs). Este ambiente imita a arquitetura ibm Z Series. Pode alojar uma variedade de sistemas operativos ou instalações da Série Z (também chamadas Deexemplos ou Pacotes Z), que são disponibilizados através de pacotes personalizados chamados IBM Application Developers Controlled Distributions (ADCDs).

Este artigo mostra-lhe como configurar uma instância ADCD num ambiente zD&T em Azure. Os ADCDs criam implementações completas do sistema operativo série Z para ambientes de desenvolvimento e teste que funcionam em zD&T.

Tal como o zD&T, os ADCDs estão disponíveis apenas para clientes e parceiros da IBM e são exclusivamente para fins de desenvolvimento e teste. Não devem ser utilizados para ambientes de produção. Numerosos pacotes de instalação IBM estão disponíveis para download através [de Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) ou IBM [PartnerWorld](https://www.ibm.com/partnerworld/public).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O [ambiente zD&T][ibm-install-z] previamente criado em Azure. Este artigo assume que está a usar a mesma imagem Ubuntu 16.04 VM criada anteriormente.

- Acesso aos meios de comunicação da ADCD através do IBM PartnerWorld ou Da Vantagem de Passaportes.

- Um [servidor de licenciamento.](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html) Isto é necessário para executar IBM zD&T. A forma como o cria depende da forma como licencia o software da IBM:

  - **O servidor de licenciamento baseado em hardware** requer um dispositivo de hardware USB que contenha os Tokens Racionais necessários para aceder a todas as partes do software. Deve obter isto da IBM.

  - **O servidor de licenciamento baseado em software** requer que você configurar um servidor centralizado para a gestão das chaves de licenciamento. Este método é preferido e requer que configurar as chaves que recebe da IBM no servidor de gestão.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Descarregue os pacotes de instalação da Passport Advantage

É necessário o acesso aos meios de comunicação DaDCD. Os passos abaixo assumem que são clientes da IBM e podem usar a Vantagem de Passaporte. Os parceiros da IBM podem usar o [IBM PartnerWorld.](https://www.ibm.com/partnerworld/public)

> [!NOTE]
> Este artigo assume que um PC do Windows é usado para aceder ao portal Azure e para descarregar os meios IBM. Se estiver a utilizar um ambiente de trabalho Mac ou Ubuntu, os comandos e o processo de obtenção dos meios de comunicação IBM podem diferir ligeiramente.

1. Inicie sessão na [Vantagem do Passaporte](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Selecione **Downloads** de Software e **Acesso aos Media**.

3. Selecione oferta de programa e número de **acordo,** e clique **Em Continuar**.

4. Introduza a descrição da peça ou o número da peça e clique em **Finder**.

5. Opcionalmente, clique na lista de encomendas alfabéticas para visualizar e ver o produto pelo nome.

6. Selecione **todos os sistemas operativos** no **campo do sistema operativo**e todos os **idiomas** no **campo Idiomas**. Então, clique em **Ir.**

7. Clique em **Selecionar ficheiros individuais** para expandir a lista e mostrar os meios individuais para descarregar.

8. Verifique o (s) pacote(s) que pretende descarregar, selecione **Download,** e depois descarregue os ficheiros para o diretório que deseja.

## <a name="upload-the-adcd-packages"></a>Faça upload do pacote aDCD

Agora que tem o(s pacote', tem de os enviar para o seu VM no Azure.

1. No portal Azure, inicie uma sessão de **SSH** com o Ubuntu VM que criou. Vá ao seu VM, selecione a lâmina **de visão geral** e, em seguida, selecione **Connect**.

2. Selecione o separador **SSH** e, em seguida, copie o comando SSH para a área de reparação.

3. Inicie sessão no seu VM usando as suas credenciais e o [cliente ssh](/azure/virtual-machines/linux/use-remote-desktop) de eleição. Esta demonstração utiliza as extensões Linux para o Windows 10, o que adiciona uma concha de corte ao pedido de comando do Windows. Putty também funciona.

4. Quando iniciar sessão, crie um diretório para carregar os pacotes IBM. Lembre-se que Linux é sensível ao caso. Por exemplo, esta demonstração pressupõe que os pacotes são enviados para:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Faça upload dos ficheiros utilizando um cliente SSH, como[o WinSCP](https://winscp.net/eng/index.php). Uma vez que o SCP faz parte do SSH, utiliza a porta 22, que é o que o SSH utiliza. Se o seu computador local não for Windows, pode escrever o [comando scp](http://man7.org/linux/man-pages/man1/scp.1.html) na sua sessão SSH.

6. Inicie o upload para o diretório Azure VM que criou, que se torna o armazenamento de imagem para zD&T.

    > [!NOTE]
    > Certifique-se de que **a ADCDTOOLS. O XML** está incluído no upload para o diretório **home/MyUserID/ZDT/adcd/nov2017.** Precisará dele mais tarde.

7. Aguarde que os ficheiros carreguem, o que pode demorar algum tempo dependendo da sua ligação ao Azure.

8. Quando os uploads estiverem completos, navegue para o diretório de volumes e descomprima todos os volumes **gz:**

    ```
        gunzip \*.gz
    ```
    
![Explorador de ficheiros mostrando volumes gz descomprimidos](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Configure o armazenamento de imagem

O próximo passo é configurar o zD&T para utilizar o ou os pacotes carregados. O processo de armazenamento de imagem dentro do zD&T permite-lhe montar e utilizar as imagens. Pode utilizar SSH ou FTP.

1. Inicie o **zDTServer**. Para isso, deve estar no nível de raiz. Insira os seguintes dois comandos por ordem:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Note a saída de URL pelo comando e utilize este URL para aceder ao servidor web. Parece semelhante a:
     > https://(o seu nome VM ou endereço IP):9443/ZDTMC/index.html
     >
     > Lembre-se, o seu acesso à web utiliza a porta 9443. Use isto para iniciar sessão no servidor web. O ID do utilizador para ZD&T é **zdtadmin** e a palavra-passe é **palavra-passe**.

    ![IBM zD&T Enterprise Edition Welcome screen](media/02-welcome.png)

3. Na página **De Início Rápido,** em **Configurar, selecione**Image **Storage**.

     ![IBM zD&T Enterprise Edition Quick Start](media/03-quickstart.png)

4. Na página de armazenamento de **imagem Configure, selecione** O Protocolo de **Transferência de Ficheiros SSH**.

5. Para **o nome anfitrião,** digite **O Localhost** e insira o caminho do diretório para onde fez o upload das imagens. Por exemplo, /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Introduza o ID do **utilizador** e a **palavra-passe** para o VM. Não utilize o ID e a palavra-passe do utilizador ZD&T.

7. Teste a ligação para se certificar de que tem acesso e, em seguida, selecione **Guardar** para salvar a configuração.

## <a name="configure-the-target-environments"></a>Configure os ambientes-alvo

O próximo passo é configurar o ambiente-alvo zD&T. Este ambiente emulado é onde as suas imagens funcionam.

1. Na página **De Início Rápido,** em **Configurar, selecione** **ambientes target**.

2. Na página de **ambientes-alvo configure, selecione** **Adicionar Target**.

3. Selecione **Linux**. A IBM suporta dois tipos de ambientes, Linux e Cloud(OpenStack), mas esta demonstração funciona em Linux.

4. Na página adicionar o **ambiente alvo,** para **nome anfitrião,** insira **o hospedeiro local**. Mantenha a **porta SSH** definida para **22**.

5. Na caixa de **etiquetas Target Environment,** introduza uma etiqueta como **myCICS.**

     ![Adicionar ecrã de ambiente alvo](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Configure a ADCD e implemente

Depois de completar os passos de configuração anteriores, deve configurar o zD&T para utilizar o (s) pacote e o ambiente-alvo. Mais uma vez, utiliza o processo de armazenamento de imagem em zD&T, que lhe permite montar e utilizar as imagens. Pode utilizar SSH ou FTP.

1. Na página **De Início Rápido,** em **Configuração, selecione** **ADCD**. Um conjunto de instruções aparecem, informando-lhe os passos que precisam de ser concluídos antes de um pacote ADCD poder ser montado. Isto explica porque nomeámos o directório-alvo como fizemos antes.

2. Assumindo que todas as imagens foram enviadas para os diretórios corretos, clique na imagem do link **ADCD** exibido no lado inferior direito (mostrado no passo 7 na seguinte imagem).

     ![IBM zD&T Enterprise Edition - Configure Ecrã ADCD](media/05-adcd.png)

## <a name="create-the-image"></a>Criar a imagem

Quando o passo de configuração anterior estiver completo, aparece uma imagem utilizando a página **De componentes ADCD.**

1. Selecione o volume (nov 2017 neste caso) para exibir os diferentes pacotes que estão nesse volume.

2. Para esta demonstração, selecione Sistema de Controlo de **Informações do Cliente (CICS) - 5.3**.

3. Na caixa de nome sinuosa, digite um nome para a imagem como **MyCICS Image**. **Image name**

4. Selecione o botão **Criar imagem** na direita inferior.

     ![IBM zD&T Enterprise Edition - Crie uma imagem usando o ecrã de componentes ADCD](media/06-adcd.png)

5. Na janela que aparece, dizendo-lhe que a imagem foi implementada com sucesso, escolha **imagens de Implantação**.

6. Na implementação de uma imagem para uma página de **ambiente-alvo,** selecione a imagem que criou na página anterior (**MyCICS Image**) e o ambiente-alvo criado anteriormente **(MyCICS).**

7. No ecrã seguinte, forneça as suas credenciais para o VM (isto é, não a credencial de ztadmin).

8. No painel Propriedades, insira o número de **processadores Centrais (CPs),** a quantidade de memória do **Sistema (GB)** e o **diretório** de implementação para a imagem em execução. Como isto é uma demonstração, mantenha-a pequena.

9. Certifique-se de que a caixa está selecionada para **emitir automaticamente o comando IPL para z/OS após a sua implantação**.

     ![Tela de propriedades](media/07-properties.png)

10. Selecione **Complete**.

11. Selecione **Implementar imagem** da imagem de implantar uma imagem para uma página de **ambiente alvo.**

A sua imagem pode agora ser implantada e está pronta para ser montada por um emulador terminal 3270.

> [!NOTE]
> Se receber um erro dizendo que não tem espaço suficiente para o disco, note que a região requer 151 Gb.

Parabéns! Está agora a gerir um ambiente de mainframe da IBM no Azure.

## <a name="learn-more"></a>Saiba mais

- [Migração mainframe: mitos e factos](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale on Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Resolução de problemas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Desmistificando o mainframe para a migração de Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
