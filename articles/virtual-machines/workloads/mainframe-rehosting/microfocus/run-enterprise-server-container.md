---
title: Executar Micro Focus Enterprise Server 4.0 num contentor do Docker em máquinas virtuais do Azure
description: Realojar suas cargas de trabalho de mainframe do IBM z/OS através da execução Micro Focus Enterprise Server num contentor do Docker em máquinas de virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896599"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Executar Micro Focus Enterprise Server 4.0 num contentor do Docker no Azure

Pode executar Micro foco Enterprise Server 4.0 num contentor do Docker no Azure. Este tutorial mostra-lhe como. Ele usa a demonstração de acctdemo Windows CICS (sistema de controle de informação de clientes) para o Enterprise Server.

Docker adiciona o isolamento e portabilidade às aplicações. Por exemplo, pode exportar uma imagem do Docker a partir de uma VM do Windows para executar no outro, ou a partir de um repositório para um servidor do Windows com o Docker. A imagem do Docker é executado na nova localização com a mesma configuração — sem ter de instalar o servidor empresarial. É parte da imagem. Considerações sobre o licenciamento ainda são aplicáveis.

Este tutorial instala o **Windows 2016 Datacenter com contentores** máquina virtual (VM) do Azure Marketplace. Esta VM inclui **Docker 18.09.0**. Os passos abaixo mostram-lhe como implementar o contentor, executá-lo e, em seguida, ligá-lo com um emulador 3270 direcionado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique estes pré-requisitos:

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O software do Micro Focus e uma licença válida (ou licença de avaliação). Se for um cliente existente do Micro Focus, contacte o seu representante do Micro Focus. Caso contrário, [peça uma avaliação](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Os arquivos de demonstração de Docker estão incluídos no Enterprise Server 4.0. Este tutorial utiliza ent\_servidor\_dockerfiles\_4.0\_windows.zip. Aceder a partir do mesmo local que acessou o ficheiro de instalação do Enterprise Server, ou aceda ao *Micro Focus* para começar a utilizar.

- A documentação para [Enterprise Server e o Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Criar uma VM

1. Proteger o suporte de dados a partir do ent\_servidor\_dockerfiles\_4.0\_windows.zip ficheiro. Proteger o ficheiro de licenciamento de ES-Docker-Prod-XXXXXXXX.mflic (obrigatório para criar as imagens do Docker).

2. Crie a VM. Para tal, abra selecione portal, do Azure **criar um recurso** na parte superior esquerda e filtrar por *do windows server*. Nos resultados, selecione **Windows Server 2016 Datacenter – com contentores**.

     ![Resultados de pesquisa de portal do Azure](media/01-portal.png)

3. Para configurar as propriedades da VM, escolha os detalhes da instância:

    1. Selecionar o tamanho da VM. Para este tutorial, considere a utilização de um **Standard DS2\_v2** VM com 2 vCPUs e 7 GB de memória.

    2. Selecione o **região** e **grupo de recursos** que pretende implementar.

    3. Para **opções de disponibilidade**, utilize a predefinição.

    4. Para **nome de utilizador**, digite a conta de administrador que pretende utilizar e a palavra-passe.

    5. Certifique-se **porta 3389 RDP** está aberta. Apenas esta porta precisa ser exposto publicamente, portanto, pode iniciar sessão na VM. Em seguida, aceite todos os valores padrão e clique em **rever + criar**.

     ![Criar um painel de máquina virtual](media/container-02.png)

4. Aguarde pela conclusão (alguns minutos) da implementação. Uma mensagem indica que a VM foi criada.

5. Clique em **Ir para recurso** Ir para o **descrição geral** painel para a sua VM.

6. À direita, clique nas **Connect** botão. O **ligar à máquina virtual** opções aparecem no lado direito.

7. Clique nas **baixar arquivo RDP** botão para transferir o ficheiro RDP que permite que anexe à VM.

8. Depois do ficheiro foi concluída a transferência, abra ele e escreva o nome de utilizador e palavra-passe que criou para a VM.

     > [!NOTE]
     > Não utilize credenciais da sua empresa para iniciar sessão. (O cliente RDP pressupõe que pretende utilizá-las. Não.)

9.  Selecione **mais opções**, em seguida, selecione as suas credenciais VM.

Neste momento, a VM está em execução e ligado através de RDP. Tem sessão iniciada e prontas para a próxima etapa.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Criar um diretório de área restrita e carregar o ficheiro zip

1.  Crie um diretório na VM em que pode carregar os ficheiros de licença e de demonstração. Por exemplo, **c:\\Sandbox**.

2.  Carregue **ent\_server\_dockerfiles\_4.0\_windows.zip** e a **ES-Docker-Prod-XXXXXXXX.mflic** ficheiro para o diretório que criou.

3.  Extraia os conteúdos do ficheiro zip para o **ent\_server\_dockerfiles\_4.0\_windows** diretório criado pelo processo de extração. Este diretório inclui um arquivo Leiame (como o arquivo. HTML e. txt) e dois subdiretórios, **EnterpriseServer** e **exemplos**.

4.  Cópia **ES-Docker-Prod-XXXXXXXX.mflic** à unidade c:\\Sandbox\\ent\_servidor\_dockerfiles\_4.0\_windows\\ EnterpriseServer e c:\\Sandbox\\ent\_servidor de\_dockerfiles\_4.0\_windows\\exemplos\\diretórios do CICS.

> [!NOTE]
> Certifique-se de que copiar o ficheiro de licenciamento para os dois diretórios. São necessários para o passo de compilação do Docker para se certificar de que as imagens estão devidamente licenciadas.

## <a name="check-docker-version-and-create-base-image"></a>Verificar a versão do Docker e crie a imagem base

> [!IMPORTANT]
> Criar a imagem apropriada do Docker é um processo de dois passos. Primeiro, crie a imagem base do Enterprise Server 4.0. Em seguida, criar outra imagem para o x64 plataforma. Embora seja possível criar um x86 (32-bit) de imagem, utilize a imagem de 64 bits.

1. Abra uma linha de comandos.

2. Certifique-se de que o Docker está instalado. Na linha de comandos, escreva:

    ```
        docker version
    ```

     Por exemplo, a versão foi 18.09.0 quando este foi escrito.

3. Para alterar o diretório, escreva **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**.

4. Tipo **bld.bat IacceptEULA** para iniciar o processo de compilação para a imagem de base inicial. Aguarde alguns minutos para que este processo seja executado. Nos resultados, tenha em atenção as duas imagens que foram criados — um para x64 e outro para x86:

     ![Janela de comando que mostra imagens](media/container-04.png)

5. Para criar a imagem final para a demonstração do CICS, mude para o diretório do CICS, escrevendo **cd\\Sandbox\\ent\_servidor\_dockerfiles\_4.0\_windows\\ Exemplos\\CICS**.

6. Para criar a imagem, escreva **bld.bat x64**. Aguarde alguns minutos para que o processo executar e a mensagem a indicar que a imagem foi criada.

7. Tipo **imagens do docker** para apresentar uma lista de todas as imagens do Docker instaladas na VM. Certifique-se **microfocus/es-acctdemo** é uma delas.

     ![Janela de comando que mostra a imagem de es-acctdemo](media/container-05.png)

## <a name="run-the-image"></a>Execute a imagem 

1.  Para iniciar o Enterprise Server 4.0 e a aplicação de acctdemo, com o tipo de linha de comandos:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Instalar como um emulador do terminal 3270 [x3270](http://x3270.bgp.nu/) e utilizá-lo para anexar, por meio da porta 9040, para a imagem que está em execução.

3.  Obter o endereço IP do contentor acctdemo, para que o Docker pode atuar como um servidor DHCP para os contentores que gere:

    1.  Obtenha o ID de contentor em execução. Tipo **Docker ps** na linha de comandos e tenha em atenção o ID (**22a0fe3159d0** neste exemplo). Guarde-o para o passo seguinte.

    2.  Para obter o endereço IP para o contentor de acctdemo, utilize o ID de contentor do passo anterior da seguinte forma:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Por exemplo:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Tenha em atenção o endereço IP para a imagem de acctdemo. Por exemplo, o endereço na seguinte saída é 172.19.202.52.

     ![Janela de comando que mostra o endereço IP](media/container-06.png)

5. Monte a imagem a utilizar o emulador. Configure o emulador para usar o endereço da imagem de acctdemo e porta 9040. Aqui, ele possui **172.19.202.52:9040**. Seu será semelhante. O **início de sessão para CICS** é aberto o ecrã.

    ![Início de sessão ao ecrã CICS](media/container-07.png)

6. Inicie sessão para a região do CICS introduzindo **SYSAD** para o **USERID** e **SYSAD** para o **palavra-passe**.

7. Limpe a tela, usando keymap do emulador. Para x3270, selecione o **Keymap** opção de menu.

8. Para iniciar o aplicativo de acctdemo, escreva **ACCT**. É apresentado o ecrã inicial para a aplicação.

     ![Ecrã de demonstração de conta](media/container-08.png)

9. Experimente com tipos de conta de exibição. Por exemplo, digite **1!d** para o pedido e **11111** para o **conta**. Outros números de conta para tentar são 22222 33333 e assim por diante.

     ![Ecrã de demonstração de conta](media/container-09.png)

10. Para apresentar a consola de administração do servidor de Enterprise, vá para a linha de comandos e o tipo **iniciar http:172.19.202.52:86**

     ![Consola de administração de servidor empresarial](media/container-010.png)

Já está! Agora está em execução e a gerir uma aplicação do CICS num contentor do Docker.

## <a name="next-steps"></a>Passos Seguintes

- [Instalar o Micro Focus Enterprise Server 4.0 e o Enterprise Developer 4.0 no Azure](./set-up-micro-focus-azure.md)
- [Migração de aplicativos de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
