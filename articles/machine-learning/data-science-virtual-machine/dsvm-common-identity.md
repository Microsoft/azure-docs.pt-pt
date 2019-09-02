---
title: Configurar uma identidade comum
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como criar contas de utilizador comuns que podem ser utilizadas em vários dados de máquinas virtuais de ciência. Pode utilizar o Azure Active Directory ou um Active Directory no local para autenticar os utilizadores para a máquina de Virtual de ciência de dados.
keywords: IA, ferramentas de ciência de dados, a máquina de virtual de ciência de dados, a análise geoespacial, o processo de ciência de dados de equipa, aprendizagem aprofundada
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208137"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Configurar uma identidade comum em um Máquina Virtual de Ciência de Dados

Em um Microsoft Azure VM (máquina virtual), incluindo um Máquina Virtual de Ciência de Dados (DSVM), você cria contas de usuário local ao provisionar a VM. Os usuários autenticados, em seguida, para a VM ao utilizar estas credenciais. Se você tiver várias VMs que seus usuários precisam acessar, o gerenciamento de credenciais poderá ficar muito complicado. Uma solução excelente é implantar contas de usuário e gerenciamento comuns por meio de um provedor de identidade baseado em padrões. Por meio dessa abordagem, você pode usar um único conjunto de credenciais para acessar vários recursos no Azure, incluindo vários DSVMs.

Active Directory é um provedor de identidade popular e tem suporte no Azure como um serviço de nuvem e como um diretório local. Pode utilizar o Azure Active Directory (Azure AD) ou no local do Active Directory para autenticar os utilizadores no DSVM autónomo ou um cluster de DSVMs num conjunto de dimensionamento de máquina virtual do Azure. Pode fazê-lo ao associar as instâncias DSVM a um domínio do Active Directory.

Se você já tiver Active Directory, poderá usá-lo como seu provedor de identidade comum. Se você não tiver Active Directory, poderá executar uma instância de Active Directory gerenciada no Azure por meio de [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS).

A documentação do [Azure ad](https://docs.microsoft.com/azure/active-directory/) fornece [instruções de gerenciamento](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)detalhadas, incluindo diretrizes sobre como conectar o Azure AD ao seu diretório local, se você tiver um.

Este artigo descreve como configurar um serviço de domínio Active Directory totalmente gerenciado no Azure usando o AD DS do Azure. Em seguida, você pode ingressar seu DSVMs no domínio Active Directory gerenciado. Essa abordagem permite que os usuários acessem um pool de DSVMs (e outros recursos do Azure) por meio de uma conta de usuário e credenciais comuns.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configurar um domínio do Active Directory totalmente gerido no Azure

O Azure AD DS torna simples para gerir as identidades ao fornecer um serviço completamente gerido no Azure. Este domínio do Active Directory, gerir utilizadores e grupos. Para configurar um domínio Active Directory hospedado pelo Azure e contas de usuário em seu diretório, siga estas etapas:

1. No portal do Azure, adicione o utilizador para o Active Directory: 

   1. Entre no centro de [Administração do Azure Active Directory](https://aad.portal.azure.com) usando uma conta que seja um administrador global do diretório.
    
   1. Selecione **Azure Active Directory** e, em seguida, **Utilizadores e grupos**.
    
   1. Em **usuários e grupos**, selecione **todos os usuários**e, em seguida, selecione **novo usuário**.
   
           The **User** pane opens:
      
      ![O painel de "Usuário"](./media/add-user.png)
    
   1. Introduza os detalhes do utilizador, como o **Nome** e o **Nome de utilizador**. A parte do nome de utilizador do nome de domínio tem de ser o predefinido inicial domínio nome "[nome de domínio] c o m" ou verificado, não federadas [nome de domínio personalizado](../../active-directory/add-custom-domain.md) como "contoso.com".
    
   1. Copie ou, caso contrário, tenha em atenção à palavra-passe de utilizador gerada, para que possa dá-la ao utilizador, após a conclusão deste processo.
    
   1. Opcionalmente, pode abrir e preencher as informações em **Perfil**, **Grupos** ou **Função de diretório** para o utilizador. 
    
   1. Em **usuário**, selecione **criar**.
    
   1. Distribua com segurança a senha gerada para o novo usuário para que ele possa entrar.

1. Crie uma instância do Azure AD DS. Siga as instruções em [habilitar Azure Active Directory Domain Services usando o portal do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (a seção "criar uma instância e definir configurações básicas"). É importante atualizar as senhas de usuário existentes no Active Directory para que a palavra-passe no Azure AD DS está sincronizada. Também é importante adicionar o DNS ao Azure AD DS, conforme descrito em "concluir os campos na janela noções básicas do portal do Azure para criar uma instância de AD DS do Azure" nessa seção.

1. Crie uma sub-rede DSVM separada na rede virtual criada na seção "criar e configurar a rede virtual" da etapa anterior.
1. Crie uma ou mais instâncias DSVM na sub-rede DSVM.
1. Siga as [instruções](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) para adicionar o DSVM ao Active Directory. 
1. Monte um compartilhamento de arquivos do Azure para hospedar seu diretório base ou notebook para que seu espaço de trabalho possa ser montado em qualquer computador. (Se precisar de permissões rígidas em nível de arquivo, você precisará do NFS (sistema de arquivos de rede) em execução em uma ou mais VMs.)

   1. [Criar uma partilha de ficheiros do Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Monte esse compartilhamento no DSVM do Linux. Quando você seleciona **conectar** para o compartilhamento de arquivos do Azure em sua conta de armazenamento no portal do Azure, o comando a ser executado no Shell do bash no DSVM do Linux é exibido. O comando tem esta aparência:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Por exemplo, suponha que você montou o compartilhamento de arquivos do Azure no/data/Workspace. Agora, crie diretórios para cada um de seus usuários no compartilhamento:/data/Workspace/user1,/data/Workspace/user2 e assim por diante. Criar um `notebooks` diretório na área de trabalho de cada utilizador. 
1. Criar links simbólicos para `notebooks` em `$HOME/userx/notebooks/remote`.   

Agora você tem os usuários em sua instância de Active Directory hospedados no Azure. Usando credenciais de Active Directory, os usuários podem entrar em qualquer DSVM (SSH ou JupyterHub) que tenha ingressado no Azure AD DS. Como é a área de trabalho do utilizador numa partilha de ficheiros do Azure, os utilizadores têm acesso a seus blocos de notas e outros trabalhos a partir de qualquer DSVM quando estiver a utilizar JupyterHub.

Para o dimensionamento automático, pode utilizar um conjunto para criar um conjunto de VMs que estão associados ao domínio dessa maneira e com o disco partilhado montado de dimensionamento de máquina virtual. Os usuários podem entrar em qualquer computador disponível no conjunto de dimensionamento de máquinas virtuais e ter acesso ao disco compartilhado onde seus blocos de anotações são salvos. 

## <a name="next-steps"></a>Passos seguintes

* [Armazenar em segurança as credenciais para aceder a recursos na cloud](dsvm-secure-access-keys.md)



