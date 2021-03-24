---
title: Executar Micro Focus Enterprise Server 5.0 num recipiente Docker em Azure | Microsoft Docs
description: Neste artigo, aprenda a executar o Micro Focus Enterprise Server 5.0 num contentor Docker no Microsoft Azure.
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 146fc59704719fd3aebb03b9b90a176221beea10
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950693"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>Executar Micro Focus Enterprise Server 5.0 em um recipiente Docker em Azure

Pode executar o Micro Focus Enterprise Server 5.0 num contentor Docker em Azure. Este tutorial mostra-lhe como. Utiliza a demonstração de acctdemo do Windows CICS (Customer Information Control System) para o Enterprise Server.

Docker adiciona portabilidade e isolamento às aplicações. Por exemplo, pode exportar uma imagem docker de uma máquina virtual Do Windows (VM) para funcionar noutra, ou de um repositório para um servidor windows com Docker. A imagem do Docker é executado na nova localização com a mesma configuração - sem ter de instalar o Enterprise Server. É parte da imagem. As considerações de licenciamento ainda se aplicam.

Este tutorial instala o **Datacenter Windows 2016 com contentores** VM do Azure Marketplace. Este VM inclui **Docker 18.09.0**. Os passos que se seguem mostram-lhe como colocar o recipiente, executá-lo e, em seguida, ligá-lo a ele com um emulador 3270.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confira estes pré-requisitos:

-   Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

-   O software Micro Focus e uma licença válida (ou licença de julgamento). Se for um cliente micro focus existente, contacte o seu representante da Micro Focus. Caso contrário, [solicite um julgamento.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)

    > [!Note] 
    > Os ficheiros de demonstração do Docker estão incluídos no Enterprise Server 5.0. Este tutorial utiliza \_ \_ estivadores de servidor de \_ 5.0 \_windows.zip. Aceda-o a partir do mesmo local onde acedeu ao ficheiro de instalação do Enterprise Server ou vá ao *Micro Focus* para começar.

-   A documentação para [o Enterprise Server e Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#%22).

## <a name="create-a-vm"></a>Criar uma VM

1.  Proteja os meios de comunicação do \_ estivador do servidor \_ de \_ 5.0 \_windows.zip ficheiro. Fixe o ficheiro de licenciamento ES-Docker-Prod-XXXXXXX.mflic (necessário para construir as imagens Docker).

2.  Criar o VM. Para isso, abra o portal Azure, **selecione Criar um recurso** a partir do menu superior esquerdo e filtrar pelo sistema operativo do servidor *windows*. Nos resultados, selecione **Windows Server.** No ecrã seguinte, selecione **O Centro de Dados do Windows Server 2016 - com recipientes**.

    ![Screenshot dos resultados da pesquisa do portal Azure](./media/run-image-1.png)

3.  Para configurar as propriedades para o VM, escolha detalhes da instância:

    1.  Selecionar o tamanho da VM. Para este tutorial, considere utilizar um **DS2 \_ v3** VM standard com 2 vCPUs e 16 GB de memória.

    2.  Selecione o **Grupo de Regiões** e **Recursos** para o qual pretende implementar.

    3.  Para **opções de disponibilidade,** utilize a definição predefinição.

    4.  Para **o nome de utilizador,** digite a conta de administrador que pretende utilizar e a palavra-passe.

    5.  Certifique-se de que a **porta 3389 RDP** está aberta. Só esta porta precisa de ser exposta publicamente, para que possa inscrever-se no VM. Em seguida, aceite todos os valores predefinidos e clique em **Rever + criar**.

    ![Screenshot de Criar um painel de máquina virtual](./media/run-image-2.png)

4.  Aguarde que a colocação termine (alguns minutos). Uma mensagem diz que o seu VM foi criado.

5.  Selecione **Ir ao Recurso** para ir à lâmina de **visão geral** para o seu VM.

6.  À direita, selecione **Connect**. As opções **de ligar à máquina virtual** aparecem à direita.

7.  Selecione o botão **Download RDP File** para descarregar o ficheiro de protocolo remoto de ambiente de trabalho (RDP) que lhe permite anexar ao VM.

8.  Depois de o ficheiro ter terminado o download, abra-o e escreva o nome de utilizador e a palavra-passe que criou para o VM.

    > [!Note]    
    > Não utilize as suas credenciais corporativas para iniciar snu. (O cliente RDP assume que pode querer usá-los. Não tem.)

9.  Selecione **Mais Escolhas** e, em seguida, selecione as suas credenciais VM.

Neste momento, o VM está a funcionar e a ser anexado via RDP. Está inscrito e pronto para o próximo passo.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Crie um diretório de caixa de areia e carrehe o ficheiro zip

1.  Crie um diretório no VM onde pode fazer o upload da demonstração e dos ficheiros de licença. Por exemplo, **C: \\ Caixa de areia**.

2.  O **\_ estivador de servidor de entfilés \_ \_ 5.0 \_windows.zip** e o ficheiro **ES-Docker-Prod-XXXXXXX.mflic** para o diretório que criou.

3.  Extrair o conteúdo do ficheiro zip para o **estivador do servidor de entfiliza \_ \_ \_ 5.0 \_ o** diretório de janelas criado pelo processo de extração. Este diretório inclui um ficheiro readme (como .html e .txt ficheiro) e duas subdiretorias, **EnterpriseServer** e **Exemplos**.

4.  Copiar **ES-Docker-Prod-XXXXXXX.mflic** para o C: \\ \\ Estivador de servidor de sandbox entfile \_ \_ \_ 5.0 \_ windows \\ EnterpriseServer e C: \\ Sandbox \\ ent server \_ \_ dockerfiles \_ 5.0 \_ windows \\ Exemplos \\ CICS diretórios.  
      
    > [!Note]
    > Certifique-se de copiar o ficheiro de licenciamento para ambos os diretórios. São necessários para que o Docker construa um passo para se certificar de que as imagens estão devidamente licenciadas.

## <a name="check-docker-version-and-create-base-image"></a>Verifique a versão Docker e crie imagem base

> [!Important]  
> Criar a imagem apropriada do Docker é um processo em duas etapas. Primeiro, crie a imagem base do Enterprise Server 5.0. Em seguida, crie outra imagem para a plataforma x64. Embora possa criar uma imagem x86 (32 bits), use a imagem de 64 bits.

1.  Abra uma linha de comandos.

2.  Verifique se o Docker está instalado. No pedido de comando, **escreva: versão estivador**  
    Por exemplo, a versão era de 18.09.0 quando esta foi escrita.

3.  Para alterar o diretório, escreva:  
    **cd \\ Estivador de servidor de \\ \_ sandbox \_ \_ entfiles 5.0 \_ windows \\ EnterpriseServer**.

4.  Tipo **bld.bat IacceptEULA** para iniciar o processo de construção para a imagem base inicial. Espere alguns minutos para que este processo desemisse. Nos resultados, note as duas imagens que foram criadas — uma para x64 e outra para x86:

    ![Janela de comando mostrando imagens](./media/run-image-3.png)

5.  Para criar a imagem final para a demonstração do CICS, mude para o diretório CICS digitando **o servidor de cd \\ Sandbox \\ ent \_ \_ estiva \_ 5.0 \_ \\ janelas Exemplos \\ CICS**.

6.  Para criar a imagem, escreva **bld.bat x64**. Espere alguns minutos para que o processo seja executado e a mensagem dizendo que a imagem foi criada.

7.  Digite imagens de **estivador** para exibir uma lista de todas as imagens docker instaladas no VM. Certifique-se de que **o microfocus/es-acctdemo** é um deles.

    ![Janela de comando mostrando imagem es-acctdemo](./media/run-image-4.png)

## <a name="run-the-image"></a>Executar a imagem

1.  Para lançar o Enterprise Server 5.0 e a aplicação acctdemo, no tipo de pedido de comando:

    ```
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ```

1.  Instale um emulador de terminal [3270, como o x3270,](http://x3270.bgp.nu/) e use-o para anexar, através da porta 9040, a imagem que está em funcionamento.

2.  Obtenha o endereço IP do recipiente de acctdemo para que o Docker possa funcionar como um servidor dinâmico do Protocolo de Configuração do Anfitrião (DHCP) para os contentores que gere:

    1.  Pegue a identificação do recipiente de corrida. Digite **Docker ps** no comando e note o ID **(22a0fe3159d0** neste exemplo). Guarde para o próximo passo.

    2.  Para obter o endereço IP para o recipiente de acctdemo, utilize o ID do recipiente do passo anterior da seguinte forma:

    ```
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

    Por exemplo:

    ```
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

4. Note o endereço IP para a imagem do acctdemo. Por exemplo, o endereço na seguinte saída é 172.19.202.52.

    ![Screenshot da janela do comando mostrando o endereço IP](./media/run-image-5.png)

5. Monte a imagem utilizando o emulador. Configure o emulador para utilizar o endereço da imagem acctdemo e da porta 9040. Aqui, é **172.19.202.52:9040**. O seu será semelhante. O sinal no ecrã **CICS** abre.

    ![Screenshot de Signon para o CICS](./media/run-image-6.png)

6. Inscreva-se na Região CICS, introduzindo **o SYSAD** para o **USERID** e **o SYSAD** para a **palavra-passe.**

7. Limpe o ecrã utilizando o mapa de chaves do emulador. Para x3270, selecione a opção **menu Keymap.**

8. Para lançar a aplicação acctdemo, escreva **ACCT**. O ecrã inicial da aplicação é apresentado.

     ![A imagem mostra uma janela da consola que mostra a aplicação.](./media/run-image-7.png)

9. Experimente com tipos de conta de exibição. Por exemplo, tipo **D** para o Pedido e **11111** para a **conta**. Outros números de conta para tentar são 22222, 33333, e assim por diante.

    ![A screenshot mostra a edição de diferentes valores na aplicação.](./media/run-image-8.png)

10. Para exibir a consola Enterprise Server Administration, aceda ao pedido de comando e **escreva http:172.19.202.52:86**.

    ![Consola de administração de servidores empresariais](media/run-image-9.png)

Já está! Agora estás a gerir e a gerir uma aplicação do CICS num contentor do Docker.

## <a name="next-steps"></a>Passos seguintes

-   [Instalar Micro Focus Enterprise Server 5.0 e Enterprise Developer 5.0 em Azure](./set-up-micro-focus-azure.md)

-   [Migração de aplicações de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
