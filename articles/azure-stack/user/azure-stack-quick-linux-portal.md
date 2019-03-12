---
title: Azure Stack início rápido - criar Portal da VM
description: Rápido de pilha do Azure - criar uma VM do Linux com o portal
services: azure-stack
cloud: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.custom: mvc
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: d9a17bdad0bfa4965d36fbe2a2cf3a7890060e98
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772573"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Início rápido: criar uma máquina de virtual de servidor do Linux com o portal do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode criar uma máquina virtual do Ubuntu Server 16.04 LTS com o portal do Azure Stack. Siga os passos neste artigo para criar e utilizar uma máquina virtual. Este artigo também dá-lhe os passos para:

* Ligar à máquina virtual com um cliente remoto.
* Instale um servidor web NGINX.
* Limpe seus recursos.

> [!NOTE]  
> As imagens de ecrã neste artigo são atualizadas de acordo com as alterações introduzidas com a versão do Azure Stack 1808. 1808 adiciona suporte para o uso *discos geridos* , além de discos não geridos. Se utilizar uma versão anterior, algumas imagens para tarefas como a seleção de disco serão diferentes do que o que é apresentado neste artigo.  


## <a name="prerequisites"></a>Pré-requisitos

* **Uma imagem do Linux no mercado do Azure Stack**

   Por predefinição, o mercado do Azure Stack não tem uma imagem do Linux. Verifique se tem o Azure Stack operador fornece a **Ubuntu Server 16.04 LTS** imagem no seu mercado. O operador pode utilizar os passos descritos na [transferir itens do marketplace do Azure para o Azure Stack](../azure-stack-download-azure-marketplace-item.md) artigo.

* **Acesso a um cliente SSH**

   Se estiver a utilizar o Azure Stack Development Kit (ASDK), poderá não ter acesso a um cliente SSH. Se precisar de um cliente, existem diversos pacotes que incluem um cliente SSH. Por exemplo, o PuTTY inclui um cliente SSH e o gerador de chaves SSH (puttygen.exe). Para obter mais informações sobre os pacotes disponíveis, leia o seguinte artigo do Azure: [Como utilizar chaves SSH com Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Este início rápido utiliza o PuTTY para gerar as chaves SSH e para ligar à máquina virtual de servidor do Linux. Para transferir e instalar PuTTY, aceda a [ https://www.putty.org/ ](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Precisa de um par de chaves SSH para concluir todos os passos neste artigo. Se tiver um par de chaves SSH existente, pode ignorar este passo.

1. Navegue para a pasta de instalação PuTTY (a localização predefinida é ```C:\Program Files\PuTTY```) e execute ```puttygen.exe```.
2. Na janela do gerador de chave PuTTY, certifique-se a **tipo de chave a gerar** está definida como **RSA**e o **número de bits numa chave gerada** está definido como **2048**. Quando estiver pronto, clique em **gerar**.

   ![Gerador de chave de configuração do puTTY](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Para gerar uma chave, mova o cursor do rato aleatoriamente, na janela do gerador de chave PuTTY.
4. Quando a geração de chaves é concluída, clique em **Guardar chave pública** e, em seguida, clique em **Guardar chave privada** para salvar as suas chaves em ficheiros.

   ![Resultados de gerador de chave puTTY](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Inicie sessão no portal do Azure Stack

Inicie sessão no portal do Azure Stack. O endereço do portal do Azure Stack depende em relação ao produto do Azure Stack que está a ligar:

* Para o Azure Stack Development Kit (ASDK), aceda a: https://portal.local.azurestack.external.
* Para um sistema integrado do Azure Stack, vá para o URL que o operador do Azure Stack fornecido.

## <a name="create-the-virtual-machine"></a>Criar a máquina virtual

1. Clique em **criar um recurso** no canto superior esquerdo do portal do Azure Stack.

2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
   
   ![Selecione o servidor do Linux](media/azure-stack-quick-linux-portal/select.png)
1. Clique em **Criar**.

4. Tipo de informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública de SSH**. Cole a chave pública de SSH que guardou e, em seguida, clique em **OK**.

   >[!NOTE]
 Certifique-se de que remover qualquer espaço em branco à esquerda nem à direita, para que a chave.

   ![Noções básicas do painel - Configurar a máquina virtual](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Selecione **D1** para a máquina virtual.

   ![Painel de tamanho - escolher um tamanho de máquina virtual](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Sobre o **definições** página, efetue as alterações de pretendida para as predefinições.
   
    - A partir da versão do Azure Stack 1808, pode configurar **armazenamento** onde pode optar por utilizar *discos geridos*. Antes da versão 1808 podem ser usados apenas os discos não geridos.    
      ![Configurar o armazenamento para discos geridos](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
    Quando as configurações estiver prontas, selecione **OK** para continuar.

7. Sobre o **resumo** página, clique em **OK** para iniciar a implementação da máquina virtual.  
   ![Implementar](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

1. Clique em **Connect** na página de máquina virtual. Pode encontrar a cadeia de ligação de SSH tem de se ligar à máquina virtual. 

2. Abra o PuTTY.

3. No ecrã de configuração do PuTTY, irá utilizar o **categoria** janela de se deslocar para cima ou para baixo. Desloque para baixo até **SSH**, expanda **SSH**e, em seguida, clique em **Auth**. Clique em **procurar** e escolha o ficheiro de chave privada que guardou.
   ![Ligar a máquina virtual](media/azure-stack-quick-linux-portal/putty03.PNG)

4. Deslocar para cima na **categoria** janela e clique em **sessão**.
5. Na **nome de anfitrião (ou endereço IP)** caixa, cole a cadeia de ligação apresentada no portal do Azure Stack. Neste exemplo, a cadeia de caracteres é ```asadmin@192.168.102.34```.

   ![Cadeia de ligação de configuração do puTTY](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Clique em **abra** para abrir uma sessão para a máquina virtual.

   ![Sessão de Linux](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Instalar o servidor web NGINX

Utilize os seguintes comandos do bash para atualizar as origens de pacotes e instalar o pacote NGINX mais recente na máquina virtual.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Quando terminar de instalar o NGINX, feche a sessão SSH e abrir a página de descrição geral de máquina virtual no portal do Azure Stack.

## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web

Um Grupo de segurança de rede (NSG) protege os tráfegos de entrada e de saída. Quando uma máquina virtual é criada no portal do Azure Stack, é criada uma regra de entrada na porta 22 para ligações SSH. Uma vez que esta máquina virtual aloja um servidor web, uma regra de NSG tem de ser criada para permitir o tráfego da web na porta 80.

1. Na máquina virtual **descrição geral** página, clique no nome da **grupo de recursos**.
2. Selecione o **grupo de segurança de rede** para a máquina virtual. O NSG pode ser identificado através da coluna **Tipo**.
3. No menu do lado esquerdo, sob **configurações**, clique em **regras de segurança de entrada**.
4. Clique em **Adicionar**.
5. Em **Nome**, escreva **http**. Certifique-se de que o **Intervalo da porta** está definido como 80 e a **Ação** está definida como **Permitir**.
6. Clique em **OK**

## <a name="view-the-nginx-welcome-page"></a>Ver a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 aberta na sua máquina virtual, pode acessar o servidor web usando o endereço IP público da máquina virtual. (O endereço IP público é apresentado na página de descrição geral da máquina virtual).

Abra um browser e navegue para ```http://<public IP address>```.

![Página de boas-vindas do servidor de web do NGINX](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>Limpar recursos

Limpe os recursos que não precisa mais. Para eliminar a máquina virtual e os respetivos recursos, selecione o grupo de recursos na página de máquina virtual e, em seguida, clique em **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou uma máquina de virtual do Linux server básica com um servidor web. Para saber mais sobre as máquinas virtuais do Azure Stack, avance para [considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md).
