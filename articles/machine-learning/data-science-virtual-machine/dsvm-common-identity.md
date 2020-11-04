---
title: Criar uma identidade comum
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como criar contas comuns de utilizadores que podem ser usadas em várias Máquinas Virtuais da Ciência de Dados. Pode utilizar o Azure Ative Directory ou um Ative Directory no local para autenticar os utilizadores na Máquina Virtual de Ciência de Dados.
keywords: aprendizagem profunda, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados de equipa
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 3dc6fb64f6e8695d84e292322293998e2f4cb0a3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324782"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Criar uma identidade comum numa máquina virtual de ciência de dados

Numa máquina virtual (VM) do Microsoft Azure, incluindo uma Máquina Virtual de Ciência de Dados (DSVM), cria-se contas de utilizadores locais ao auser o VM. Em seguida, os utilizadores autenticam-se no VM utilizando estas credenciais. Se tiver vários VMs a que os seus utilizadores precisam de aceder, gerir credenciais pode ficar muito complicado. Uma excelente solução é implementar contas e gestão comuns de utilizadores através de um fornecedor de identidade baseado em padrões. Através desta abordagem, você pode usar um único conjunto de credenciais para aceder a múltiplos recursos em Azure, incluindo vários DSVMs.

Ative Directy é um fornecedor de identidade popular e é apoiado no Azure tanto como um serviço de nuvem e como um diretório no local. Pode utilizar o Azure Ative Directory (Azure AD) ou no local Ative Directory para autenticar os utilizadores num DSVM autónomo ou num conjunto de DSVMs num conjunto de máquinas virtuais Azure. Fá-lo juntando as instâncias DSVM a um domínio de Diretório Ativo.

Se já tem Ative Directory, pode usá-lo como seu fornecedor de identidade comum. Se não tiver Diretório Ativo, pode executar uma instância de Diretório Ativo gerida no Azure através [dos Serviços de Domínio do Diretório Ativo Azure](../../active-directory-domain-services/index.yml) (Azure AD DS).

A documentação do [Azure AD](../../active-directory/index.yml) fornece [instruções detalhadas de gestão,](../../active-directory/hybrid/whatis-hybrid-identity.md)incluindo orientações sobre a ligação da Azure AD ao seu diretório no local, se tiver uma.

Este artigo descreve como configurar um serviço de domínio Ative Directory totalmente gerido em Azure, utilizando o Azure AD DS. Em seguida, pode juntar os seus DSVMs ao domínio gerido do Ative Directory. Esta abordagem permite que os utilizadores acedam a um conjunto de DSVMs (e outros recursos Azure) através de uma conta de utilizador comum e credenciais.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Crie um domínio de Diretório Ativo totalmente gerido no Azure

O Azure AD DS torna simples gerir as suas identidades, fornecendo um serviço totalmente gerido no Azure. Neste domínio ative Directory, gere utilizadores e grupos. Para configurar um domínio de Diretório Ativo alojado no Azure e contas de utilizador no seu diretório, siga estes passos:

1. No portal Azure, adicione o utilizador ao Ative Directory: 

   1. Inscreva-se no [Azure Ative Directory admin center](https://aad.portal.azure.com) usando uma conta que é um administrador global para o diretório.
    
   1. Selecione **Azure Active Directory** e, em seguida, **Utilizadores e grupos**.
    
   1. Em **Utilizadores e grupos** , selecione **Todos os utilizadores** e, em seguida, selecione Novo **utilizador**.
   
        O painel do **utilizador** abre:
      
        ![O painel "Utilizador"](./media/add-user.png)
    
   1. Introduza os detalhes do utilizador, como o **Nome** e o **Nome de utilizador**. A parte do nome de domínio do nome de utilizador deve ser o nome de domínio inicial "[nome de domínio].onmicrosoft.com" ou um nome de [domínio personalizado](../../active-directory/fundamentals/add-custom-domain.md) verificado e não federado, como "contoso.com".
    
   1. Copie ou, caso contrário, tenha em atenção à palavra-passe de utilizador gerada, para que possa dá-la ao utilizador, após a conclusão deste processo.
    
   1. Opcionalmente, pode abrir e preencher as informações em **Perfil** , **Grupos** ou **Função de diretório** para o utilizador. 
    
   1. No **Utilizador** , selecione **Criar**.
    
   1. Distribua a palavra-passe gerada ao novo utilizador para que possa iniciar sedu.

1. Crie uma instância AD DS Azure. Siga as instruções em  [Enable Azure Ative Directory Domain Services utilizando o portal Azure](../../active-directory-domain-services/tutorial-create-instance.md) (a secção "Criar uma instância e configurar definições básicas"). É importante atualizar as palavras-passe do utilizador existentes no Ative Directory para que a palavra-passe em Azure AD DS seja sincronizada. Também é importante adicionar DNS ao Azure AD DS, como descrito em "Complete os campos na janela Basics do portal Azure para criar uma instância AD DS AZure" nessa secção.

1. Crie uma sub-rede DSVM separada na rede virtual criada na secção "Criar e configurar a rede virtual" do passo anterior.
1. Crie uma ou mais instâncias DSVM na sub-rede DSVM.
1. Siga as [instruções](../../active-directory-domain-services/join-ubuntu-linux-vm.md) para adicionar o DSVM ao Ative Directory. 
1. Monte uma partilha de Ficheiros Azure para hospedar o seu diretório de casa ou portátil para que o seu espaço de trabalho possa ser montado em qualquer máquina. (Se precisar de permissões de nível de ficheiro apertado, necessitará do Sistema de Ficheiros de Rede [NFS] em execução em um ou mais VMs.)

   1. [Criar uma partilha de Ficheiros Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Monte esta parte no Linux DSVM. Quando selecionar **Connect** for the Azure Files partilhar na sua conta de armazenamento no portal Azure, aparece o comando para executar a casca de bash no Linux DSVM. O comando é assim:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Por exemplo, assuma que montou os seus Ficheiros Azure partilhar em /data/workspace. Agora, crie diretórios para cada um dos seus utilizadores na partilha: /data/workspace/user1, /data/workspace/user2, e assim por diante. Crie um `notebooks` diretório no espaço de trabalho de cada utilizador. 
1. Criar ligações simbólicas para `notebooks` em `$HOME/userx/notebooks/remote` .   

Tem agora os utilizadores na sua instância Ative Directory hospedados no Azure. Ao utilizar credenciais de Ative Directory, os utilizadores podem iniciar sação em qualquer DSVM (SSH ou JupyterHub) que se junte ao Azure AD DS. Como o espaço de trabalho do utilizador está numa partilha de Ficheiros Azure, os utilizadores têm acesso aos seus cadernos e outros trabalhos de qualquer DSVM quando estão a usar o JupyterHub.

Para autoscalar, pode utilizar uma balança de máquina virtual definida para criar um conjunto de VMs que estão todos unidos ao domínio desta forma e com o disco partilhado montado. Os utilizadores podem iniciar sposição em qualquer máquina disponível no conjunto de escala de máquina virtual e ter acesso ao disco partilhado onde os seus cadernos são guardados. 

## <a name="next-steps"></a>Passos seguintes

* [Armazenar credenciais de segurança para aceder a recursos na nuvem](dsvm-secure-access-keys.md)