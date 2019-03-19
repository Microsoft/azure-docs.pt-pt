---
title: Como utilizar o plug-in de subordinado do Azure com integração contínua da Hudson | Documentos da Microsoft
description: Descreve como utilizar o plug-in de subordinado do Azure com Hudson de integração contínua.
services: virtual-machines-linux
documentationcenter: ''
author: rmcmurray
manager: wpickett
editor: ''
ms.assetid: b2083d1c-4de8-4a19-a615-ccc9d9b6e1d9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: ef24e356c9ac8424fc519a3b16af5d37a20e706f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999786"
---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Como utilizar o plug-in de subordinado do Azure com integração contínua da Hudson
O plug-in para Hudson de subordinado do Azure permite-lhe aprovisionar nós subordinados no Azure, quando em execução distribuída baseia-se.

## <a name="install-the-azure-slave-plug-in"></a>Instalar o plug-in subordinado do Azure
1. No dashboard do Hudson, clique em **Hudson gerir**.
2. Na **gerir Hudson** página, clique em **gerir plug-ins**.
3. Clique nas **disponível** separador.
4. Clique em **pesquisa** e escreva **Azure** para limitar a lista de plug-ins relevantes.
   
    Se optar por percorrer a lista de plug-ins disponíveis, encontrará o subordinado do Azure Plug-in no **distribuído criar e gestão de clusters** secção a **outros** separador.
5. Selecione a caixa de verificação **Plug-in do Azure Slave**.
6. Clique em **Instalar**.
7. Reinicie Hudson.

Agora que o plug-in estiver instalado, seria os passos seguintes para configurar o plug-in com seu perfil de subscrição do Azure e criar um modelo que será utilizado na criação da VM para o nó subordinado.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configurar o plug-in subordinado do Azure com o seu perfil de subscrição
Um perfil de subscrição, também referido como publicar as definições, é um arquivo XML que contém credenciais seguras e algumas informações adicionais, que terá de trabalhar com o Azure no seu ambiente de desenvolvimento. Para configurar o plug-in de subordinado do Azure, terá de:

* O id de subscrição
* Um certificado de gestão para a sua subscrição

Estes podem ser encontrados no seu [perfil de subscrição]. Segue-se um exemplo de um perfil de subscrição.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

          <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

              ServiceManagementUrl="https://management.core.windows.net"

              Id="<Subscription ID>"

              Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

          </PublishProfile>

    </PublishData>

Assim que tiver o seu perfil de subscrição, siga estes passos para configurar o plug-in de subordinado do Azure.

1. No dashboard do Hudson, clique em **Hudson gerir**.
2. Clique em **configurar o sistema**.
3. Desloque para baixo na página para encontrar os **Cloud** secção.
4. Clique em **Adicionar nova nuvem > Microsoft Azure**.
   
    ![Adicionar nova nuvem][add new cloud]
   
    Isto irá mostrar os campos onde tem de introduzir os detalhes da sua subscrição.
   
    ![configurar o perfil][configure profile]
5. Copie o certificado de gestão e o id de subscrição do seu perfil de subscrição e cole-os nos campos apropriados.
   
    Ao copiar o certificado de gestão e o id de subscrição, **não** inclua as aspas que colocar os valores.
6. Clique em **verificar configuração**.
7. Quando a configuração for verificada com êxito, clique em **guardar**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configurar um modelo de máquina virtual para o Azure subordinado Plug-in
Um modelo de máquina virtual define os parâmetros que o plug-in irá utilizar para criar um nó subordinado no Azure. Nos passos seguintes Criaremos modelo para uma VM do Ubuntu.

1. No dashboard do Hudson, clique em **Hudson gerir**.
2. Clique em **configurar o sistema**.
3. Desloque para baixo na página para encontrar os **Cloud** secção.
4. Dentro do **Cloud** secção, encontrar **adicionar modelo de Máquina Virtual do Azure** e clique no **adicionar** botão.
   
    ![Adicionar modelo de vm][add vm template]
5. Especifique um nome de serviço em nuvem do **nome** campo. Se o nome especificado se refere a um serviço cloud existente, a VM será provisionada em que o serviço. Caso contrário, o Azure irá criar um novo.
6. Na **Descrição** , insira o texto que descreve o modelo que está a criar. Esta informação é apenas para fins de documentários e não é utilizada numa VM de aprovisionamento.
7. Na **etiquetas** , insira **linux**. Esta etiqueta é utilizada para identificar o modelo que está a criar e, em seguida, é utilizada para referenciar o modelo, ao criar uma tarefa de Hudson.
8. Selecione uma região onde a VM será criada.
9. Selecione o tamanho apropriado da VM.
10. Especifique uma conta de armazenamento onde a VM será criada. Certifique-se de que está a ser a mesma região que o serviço em nuvem, que irá utilizar. Se pretender que o novo armazenamento a ser criado, pode deixar este campo em branco.
11. Período de retenção Especifica o número de minutos antes de Hudson elimina um subordinado de inatividade. Deixe o valor padrão de 60.
12. Na **utilização**, selecione a condição apropriada quando este nó subordinado será utilizado. Por agora, selecione **utilizar este nó tanto quanto possível**.
    
     Neste momento, seu formulário teria uma aparência um pouco semelhante a este:
    
     ![configuração de modelo][template config]
13. Na **Id de família de imagem ou** tem de especificar a imagem do sistema que será instalada na sua VM. Pode selecionar a partir de uma lista de famílias de imagem ou especificar uma imagem personalizada.
    
     Se pretender selecionar numa lista de famílias de imagem, introduza o primeiro caráter (diferencia maiúsculas de minúsculas) do nome da família de imagem. Por exemplo, digitando **U** será apresentada uma lista de famílias de Ubuntu Server. Depois de selecionar a partir da lista, o Jenkins irá utilizar a versão mais recente dessa imagem de sistema dessa família quando aprovisionar a VM.
    
     ![Lista de família de SO][OS family list]
    
     Se tiver uma imagem personalizada que pretende utilizar em vez disso, introduza o nome da imagem personalizada. Os nomes de imagens personalizadas não são apresentados numa lista, para que tenha de se certificar de que o nome é introduzido corretamente.    
    
     Para este tutorial, escreva **U** para abrir uma lista de imagens do Ubuntu e selecione **Ubuntu Server 14.04 LTS**.
14. Para **método de arranque**, selecione **SSH**.
15. Copie o script abaixo e cole o **Init script** campo.
    
         # Install Java
    
         sudo apt-get -y update
    
         sudo apt-get install -y openjdk-7-jdk
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y openjdk-7-jdk
    
         # Install git
    
         sudo apt-get install -y git
    
         #Install ant
    
         sudo apt-get install -y ant
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y ant
    
     O **Init script** será executado após a VM é criada. Neste exemplo, o script instala o Java e ant git.
16. Na **nome de utilizador** e **palavra-passe** campos, introduza os valores preferenciais para a conta de administrador que será criada na sua VM.
17. Clique em **verificar modelo** para verificar se os parâmetros que especificou são válidos.
18. Clique em **Guardar**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Criar uma tarefa de Hudson que é executado num nó subordinado no Azure
Nesta seção, criará uma tarefa de Hudson que será executado num nó subordinado no Azure.

1. No dashboard do Hudson, clique em **nova tarefa**.
2. Introduza um nome para a tarefa que está a criar.
3. Para o tipo de tarefa, selecione **criar uma tarefa de software de estilo livre**.
4. Clique em **OK**.
5. Na página de configuração da tarefa, selecione **restringir onde pode ser executado neste projeto**.
6. Selecione **menu de nó e o rótulo** e selecione **linux** (especificamos esta etiqueta ao criar o modelo de máquina virtual na secção anterior).
7. Na **crie** secção, clique em **Adicionar passo de compilação** e selecione **executar shell**.
8. Edite o seguinte script, substituindo **{o nome da conta github}**, **{o seu nome de projeto}**, e **{o seu diretório de projeto}** com apropriado valores e cole o editado script na área de texto que aparece.
   
        # Clone from git repo
   
        currentDir="$PWD"
   
        if [ -e {your project directory} ]; then
   
              cd {your project directory}
   
              git pull origin master
   
        else
   
              git clone https://github.com/{your github account name}/{your project name}.git
   
        fi
   
        # change directory to project
   
        cd $currentDir/{your project directory}
   
        #Execute build task
   
        ant
9. Clique em **Guardar**.
10. No dashboard do Hudson, localizar a tarefa que acabou de criar e clique nas **agendar uma compilação** ícone.

Hudson, em seguida, irá criar um nó subordinado utilizando o modelo que criou na secção anterior e execute o script que especificou no passo de compilação para esta tarefa.

## <a name="next-steps"></a>Próximos Passos
Para obter mais informações sobre como utilizar o Azure com o Java, veja o [Centro de Programadores Java do Azure].

<!-- URL List -->

[Centro de Programadores Java do Azure]: https://azure.microsoft.com/develop/java/
[perfil de subscrição]: https://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

