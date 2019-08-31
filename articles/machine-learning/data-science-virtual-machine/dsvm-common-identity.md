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
ms.openlocfilehash: 5cd310dac28b999af3d21c46b108abdc6d5779b5
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192249"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Configure uma identidade comum na máquina de Virtual de ciência de dados

Numa máquina virtual do Azure (VM), incluindo a ciência de dados Máquina Virtual (DSVM), é possível criar contas de utilizador local durante o aprovisionamento da VM. Os usuários autenticados, em seguida, para a VM ao utilizar estas credenciais. Se tiver várias VMs que precisa acessar, essa abordagem pode rapidamente obter complicada como gerir as credenciais. Contas de utilizador comum e de gestão através de um fornecedor de identidade baseada em padrões permitem-lhe utilizar um único conjunto de credenciais para aceder a vários recursos no Azure, incluindo as DSVMs vários. 

Active Directory é um fornecedor de identidade populares e é suportado no Azure como um serviço e no local. Pode utilizar o Azure Active Directory (Azure AD) ou no local do Active Directory para autenticar os utilizadores no DSVM autónomo ou um cluster de DSVMs num conjunto de dimensionamento de máquina virtual do Azure. Pode fazê-lo ao associar as instâncias DSVM a um domínio do Active Directory. 

Se já tiver o Active Directory para gerir as identidades, pode utilizá-lo como o fornecedor de identidade comum. Se não tiver o Active Directory, pode executar uma instância do Active Directory no Azure através de um serviço chamado [do Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS). 

A documentação para [do Azure AD](https://docs.microsoft.com/azure/active-directory/) fornece detalhadas [instruções de gestão](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution), incluindo a ligação do Azure AD para o seu diretório no local, se tiver uma. 

Este artigo descreve os passos para configurar um serviço de domínio do Active Directory totalmente gerido no Azure com o Azure AD DS. Em seguida, pode associar seu DSVMs ao domínio gerido do Active Directory para permitir aos utilizadores aceder a um conjunto de DSVMs (e outros recursos do Azure), utilizando uma conta de utilizador comum e as credenciais. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configurar um domínio do Active Directory totalmente gerido no Azure

O Azure AD DS torna simples para gerir as identidades ao fornecer um serviço completamente gerido no Azure. Este domínio do Active Directory, gerir utilizadores e grupos. Os passos para configurar um contas de utilizador e domínio alojado no Azure Active Directory no seu diretório estão:

1. No portal do Azure, adicione o utilizador para o Active Directory: 

   a. Inicie sessão no [Centro de administradores do Azure Active Directory](https://aad.portal.azure.com) com uma conta que seja administrador global do diretório.
    
   b. Selecione **Azure Active Directory** e, em seguida, **Utilizadores e grupos**.
    
   c. Em **Utilizadores e grupos**, selecione **Todos os utilizadores** e, em seguida, selecione **Novo utilizador**.
   
      O **utilizador** painel abre-se.
      
      ![O painel de "Usuário"](./media/add-user.png)
    
   d. Introduza os detalhes do utilizador, como o **Nome** e o **Nome de utilizador**. A parte do nome de utilizador do nome de domínio tem de ser o predefinido inicial domínio nome "[nome de domínio] c o m" ou verificado, não federadas [nome de domínio personalizado](../../active-directory/add-custom-domain.md) como "contoso.com".
    
   e. Copie ou, caso contrário, tenha em atenção à palavra-passe de utilizador gerada, para que possa dá-la ao utilizador, após a conclusão deste processo.
    
   f. Opcionalmente, pode abrir e preencher as informações em **Perfil**, **Grupos** ou **Função de diretório** para o utilizador. 
    
   g. Em **Utilizador**, selecione **Criar**.
    
   h. Dê a palavra-passe gerada com segurança ao novo utilizador, para que este possa iniciar sessão.

1. Crie uma instância do Azure AD DS. Siga as instruções no artigo [ativar o Azure Active Directory Domain Services no portal do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (tarefas 1 a 5). É importante atualizar as senhas de usuário existentes no Active Directory para que a palavra-passe no Azure AD DS está sincronizada. Também é importante adicionar o DNS do Azure AD DS, conforme descrito na tarefa 4 do artigo. 

1. Crie uma sub-rede separada da DSVM na rede virtual criada na tarefa 2 do passo anterior.
1. Crie uma ou mais instâncias de VM de ciência de dados na sub-rede DSVM. 
1. Siga os [instruções](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) para adicionar DSVM ao Active Directory. 
1. Monte uma partilha de ficheiros do Azure para alojar o seu diretório de casa ou o bloco de notas para ativar a montagem de sua área de trabalho em qualquer máquina. (Se tiver permissões de nível de arquivo forte, precisará NFS em execução num ou mais VMs.)

   a. [Criar uma partilha de ficheiros do Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Montá-la na DSVM do Linux. Quando seleciona a **Connect** botão para partilha de ficheiros do Azure na sua conta de armazenamento no portal do Azure, o comando a executar no Bash shell na DSVM do Linux é apresentada. O comando tem esta aparência:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Partem do princípio que montar a partilha de ficheiros do Azure no /data/workspace, por exemplo. Agora, criar diretórios para cada um dos seus utilizadores na partilha: /data/workspace/user1 /data/workspace/user2 e assim por diante. Criar um `notebooks` diretório na área de trabalho de cada utilizador. 
1. Criar links simbólicos para `notebooks` em `$HOME/userx/notebooks/remote`.   

Agora, tem os utilizadores na sua instância do Active Directory alojada no Azure. Com as credenciais do Active Directory, os utilizadores podem iniciar sessão qualquer DSVM (SSH ou JupyterHub) que está associado ao Azure AD DS. Como é a área de trabalho do utilizador numa partilha de ficheiros do Azure, os utilizadores têm acesso a seus blocos de notas e outros trabalhos a partir de qualquer DSVM quando estiver a utilizar JupyterHub. 

Para o dimensionamento automático, pode utilizar um conjunto para criar um conjunto de VMs que estão associados ao domínio dessa maneira e com o disco partilhado montado de dimensionamento de máquina virtual. Os utilizadores podem iniciar sessão qualquer máquina disponível no conjunto de dimensionamento de máquina virtual e ter acesso ao disco partilhado onde são guardados os seus blocos de notas. 

## <a name="next-steps"></a>Passos Seguintes

* [Armazenar em segurança as credenciais para aceder a recursos na cloud](dsvm-secure-access-keys.md)



