---
title: Executar Micro Focus Enterprise Server 4.0 em um recipiente de Docker em máquinas virtuais Azure
description: Reacontem as suas cargas de trabalho do mainframe IBM z/OS executando o Micro Focus Enterprise Server num recipiente Docker em Máquinas Virtuais Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "61488459"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Executar Micro Focus Enterprise Server 4.0 em um contentor docker em Azure

Pode executar o Micro Focus Enterprise Server 4.0 num contentor do Docker no Azure. Este tutorial mostra-lhe como. Utiliza a demonstração de acctdemo do Windows CICS (Customer Information Control System) para o Enterprise Server.

Docker adiciona portabilidade e isolamento às aplicações. Por exemplo, pode exportar uma imagem do Docker de um VM windows para correr noutro, ou de um repositório para um servidor windows com Docker. A imagem do Docker corre no novo local com a mesma configuração — sem ter de instalar o Enterprise Server. Faz parte da imagem. As considerações de licenciamento ainda se aplicam.

Este tutorial instala o **Datacenter Windows 2016 com** máquina virtual contentores (VM) do Azure Marketplace. Este VM inclui **Docker 18.09.0**. Os passos abaixo mostram-lhe como implantar o recipiente, executá-lo e, em seguida, ligá-lo a ele com um emulador 3270.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confira estes pré-requisitos:

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O software Micro Focus e uma licença válida (ou licença de teste). Se for um cliente micro focus existente, contacte o seu representante da Micro Focus. Caso contrário, [solicite um julgamento.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)

     > [!NOTE]
     > Os ficheiros de demonstração do Docker estão incluídos no Enterprise Server 4.0. Este tutorial\_utiliza\_ficheiros\_de\_estivadores de servidores ent 4.0 windows.zip. Acede-o a partir do mesmo local onde acedeu ao ficheiro de instalação do Enterprise Server ou vá ao *Micro Focus* para começar.

- A documentação para [Enterprise Server e Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Criar uma VM

1. Proteja os meios\_de\_comunicação do ficheiro\_4.0\_windows.zip do servidor ent. Proteja o ficheiro de licenciamento ES-Docker-Prod-XXXXXXXX.mflic (necessário para construir as imagens do Docker).

2. Crie o VM. Para isso, abra o portal Azure, selecione **Criar um recurso** a partir da parte superior esquerda e filtrar pelo servidor *windows*. Nos resultados, selecione **O Datacenter do Windows Server 2016 – com contentores**.

     ![Resultados da pesquisa do portal Azure](media/01-portal.png)

3. Para configurar as propriedades para o VM, escolha detalhes da instância:

    1. Selecionar o tamanho da VM. Para este tutorial, considere utilizar um VM **Standard\_DS2 v2** com 2 vCPUs e 7 GB de memória.

    2. Selecione o Grupo **região** e **recursos** que gostaria de implementar.

    3. Para **opções de disponibilidade,** utilize a definição predefinida.

    4. Para **username,** escreva a conta de administrador que pretende utilizar e a palavra-passe.

    5. Certifique-se de que a **porta 3389 RDP** está aberta. Só esta porta precisa de ser exposta publicamente, para que possa iniciar sessão no VM. Em seguida, aceite todos os valores predefinidos e clique em **Rever+ criar**.

     ![Criar um painel de máquina virtual](media/container-02.png)

4. Aguarde a colocação para terminar (alguns minutos). Uma mensagem diz que o seu VM foi criado.

5. Clique em **Ir ao Recurso** para ir à lâmina de visão **geral** para o seu VM.

6. À direita, clique no botão **Ligar.** As opções de **Ligação à máquina virtual** aparecem à direita.

7. Clique no botão **Download RDP File** para descarregar o ficheiro RDP que lhe permite ligar ao VM.

8. Depois de o ficheiro ter terminado de descarregar, abra-o e escreva o nome de utilizador e a palavra-passe que criou para o VM.

     > [!NOTE]
     > Não use as suas credenciais corporativas para iniciar sessão. (O cliente RDP assume que pode querer usá-los. Não tem.)

9.  Selecione **Mais Escolhas**e, em seguida, selecione as suas credenciais VM.

Neste ponto, o VM está a funcionar e ligado via RDP. Está sintetizador e pronto para o próximo passo.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Crie um diretório de caixa de areia e carregue o ficheiro zip

1.  Crie um diretório no VM onde possa fazer o upload da demonstração e dos ficheiros de licença. Por exemplo, **C:\\Caixa de areia**.

2.  Carregue **os\_ficheiros\_de\_es-Docker-Prod-XXXXXX.mflic para o diretório que criou.\_** **ES-Docker-Prod-XXXXXXXX.mflic**

3.  Extrair o conteúdo do ficheiro zip para o diretório de **janelas\_do\_servidor\_\_ent 4.0** criado pelo processo de extração. Este diretório inclui um ficheiro readme (como .html e .txt file) e dois subdiretórios, **EnterpriseServer** e **Exemplos**.

4.  Copie **ES-Docker-Prod-XXXXXXXX.mflic** para\\o\\C:\_Sandbox\_ent\\\_server dockerfiles\_4.0\_windows\_EnterpriseServer\_e\\C:\\Sandbox\\ent\_server dockerfiles 4.0 windows Exemplos DIRECTI diretórios.\\

> [!NOTE]
> Certifique-se de copiar o ficheiro de licenciamento para ambos os diretórios. São necessários para que o Docker construa um passo para garantir que as imagens estão devidamente licenciadas.

## <a name="check-docker-version-and-create-base-image"></a>Verifique a versão do Docker e crie a imagem base

> [!IMPORTANT]
> Criar a imagem adequada do Docker é um processo em duas etapas. Primeiro, crie a imagem base do Enterprise Server 4.0.Em seguida, crie outra imagem para a plataforma x64. Embora possa criar uma imagem x86 (32 bits), use a imagem de 64 bits.

1. Abra uma linha de comandos.

2. Verifique se o Docker está instalado. Na linha de comandos, escreva:

    ```
        docker version
    ```

     Por exemplo, a versão era de 18.09.0 quando esta foi escrita.

3. Para alterar o diretório, digite **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**.

4. Digite **bld.bat IacceptEULA** para iniciar o processo de construção para a imagem base inicial. Espere alguns minutos para que este processo decorra. Nos resultados, note as duas imagens que foram criadas — uma para x64 e outra para x86:

     ![Janela de comando mostrando imagens](media/container-04.png)

5. Para criar a imagem final para a demonstração do CICS, mude para o diretório CICS digitando **\\cd\\Sandbox ent\_server\_dockerfiles\_4.0\_windows\\Exemplos\\CICS**.

6. Para criar a imagem, digite **bld.bat x64**. Aguarde alguns minutos para que o processo decorra e a mensagem a dizer que a imagem foi criada.

7. Digite imagens de **estivador** para exibir uma lista de todas as imagens do Docker instaladas no VM. Certifique-se de **que microfocus/es-acctdemo** é um deles.

     ![Janela de comando mostrando imagem es-acctdemo](media/container-05.png)

## <a name="run-the-image"></a>Executar a imagem 

1.  Para lançar o Enterprise Server 4.0 e a aplicação de acctdemo, no tipo de solicitação de comando:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Instale um emulador terminal 3270, como [x3270,](http://x3270.bgp.nu/) e use-o para anexar, através da porta 9040, à imagem que está em execução.

3.  Obtenha o endereço IP do recipiente de acreção, para que o Docker possa atuar como um servidor DHCP para os contentores que gere:

    1.  Pegue a identificação do recipiente de corrida. Digite **o Ps** no pedido de comando e note o ID **(22a0fe3159d0** neste exemplo). Guarde para o próximo passo.

    2.  Para obter o endereço IP do recipiente de acreção, utilize o ID do recipiente do passo anterior da seguinte forma:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Por exemplo:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Note o endereço IP para a imagem de acctdemo. Por exemplo, o endereço na seguinte saída é de 172.19.202.52.

     ![Janela de comando mostrando endereço IP](media/container-06.png)

5. Monte a imagem usando o emulador. Configure o emulador para utilizar o endereço da imagem de acctdemo e da porta 9040. Aqui, é **172.19.202.52:9040**. O seu será semelhante. O ecrã **Signon para CICS** abre.

    ![Signon para tela CICS](media/container-07.png)

6. Inscreva-se na Região DO CICS ao entrar na **SYSAD** para a **USERID** e **sYSAD** para a **palavra-passe**.

7. Limpe o ecrã, utilizando o mapa-chave do emulador. Para x3270, selecione a opção de menu **Keymap.**

8. Para lançar a aplicação de acctdemo, digite **ACCT**. O ecrã inicial da aplicação é apresentado.

     ![Ecrã de demonstração de conta](media/container-08.png)

9. Experimente os tipos de conta de exibição. Por exemplo, digite **D** para o Pedido e **11111** para a **CONTA**. Outros números de conta a tentar são 22222, 33333, e assim por diante.

     ![Ecrã de demonstração de conta](media/container-09.png)

10. Para exibir a consola da Administração do Servidor da Empresa, vá ao pedido de comando e escreva **início http:172.19.202.52:86**

     ![Consola de Administração de Servidores Empresariais](media/container-010.png)

Já está! Agora estás a gerir uma aplicação CICS num contentor do Docker.

## <a name="next-steps"></a>Passos seguintes

- [Instale micro Focus Enterprise Server 4.0 e Enterprise Developer 4.0 no Azure](./set-up-micro-focus-azure.md)
- [Migração de aplicações de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
