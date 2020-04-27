---
title: Criar uma identidade comum
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como criar contas comuns de utilizadores que podem ser usadas em várias Máquinas Virtuais de Ciência de Dados. Pode utilizar o Diretório Ativo Azure ou um Diretório Ativo no local para autenticar os utilizadores na Máquina Virtual da Ciência dos Dados.
keywords: aprendizagem profunda, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados de equipa
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "70208137"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Criar uma identidade comum numa máquina virtual de ciência de dados

Numa máquina virtual do Microsoft Azure (VM), incluindo uma Máquina Virtual de Ciência de Dados (DSVM), cria contas de utilizador locais enquanto aprovisiona o VM. Os utilizadores autenticam então o VM utilizando estas credenciais. Se tiver vários VMs a que os seus utilizadores precisam de aceder, gerir credenciais pode ficar muito complicado. Uma excelente solução é implementar contas e gestão comuns de utilizadores através de um fornecedor de identidade baseado em padrões. Através desta abordagem, você pode usar um único conjunto de credenciais para aceder a múltiplos recursos em Azure, incluindo vários DSVMs.

O Ative Directy é um fornecedor de identidade popular e é apoiado no Azure tanto como um serviço na nuvem como como um diretório no local. Pode utilizar o Azure Ative Directory (Azure AD) ou no local Ative Directory para autenticar os utilizadores num DSVM autónomo ou num conjunto de DSVMs num conjunto de máquinas virtuais Azure. Fá-lo juntando as instâncias dSVM a um domínio de Diretório Ativo.

Se já tem Diretório Ativo, pode usá-lo como fornecedor de identidade comum. Se não tiver Diretório Ativo, pode executar uma instância de Diretório Ativo gerida no Azure através do [Azure Ative Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS).

A documentação para [a AD Azure](https://docs.microsoft.com/azure/active-directory/) fornece instruções de [gestão detalhadas,](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)incluindo orientações sobre a ligação da AD Azure ao seu diretório no local, se tiver uma.

Este artigo descreve como criar um serviço de domínio Ative Directy totalmente gerido no Azure utilizando o Azure AD DS. Em seguida, pode juntar os seus DSVMs ao domínio de Diretório Ativo gerido. Esta abordagem permite aos utilizadores aceder a um conjunto de DSVMs (e outros recursos Azure) através de uma conta de utilizador comum e credenciais.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Criar um domínio de Diretório Ativo totalmente gerido em Azure

O Azure AD DS torna simples gerir as suas identidades fornecendo um serviço totalmente gerido no Azure. Neste domínio de Diretório Ativo, gere utilizadores e grupos. Para configurar um domínio de Diretório Ativo e contas de utilizador hospedados no Azure no seu diretório, siga estes passos:

1. No portal Azure, adicione o utilizador ao Ative Directy: 

   1. Inscreva-se no Centro de [Administração do Diretório Ativo Azure](https://aad.portal.azure.com) usando uma conta que é uma administração global para o diretório.
    
   1. Selecione **Azure Active Directory** e, em seguida, **Utilizadores e grupos**.
    
   1. Nos **Utilizadores e grupos**, selecione **Todos os utilizadores**e, em seguida, selecione Novo **utilizador**.
   
           The **User** pane opens:
      
      ![O painel "Utilizador"](./media/add-user.png)
    
   1. Introduza os detalhes do utilizador, como o **Nome** e o **Nome de utilizador**. A porção de nome de domínio do nome do utilizador deve ser o nome de domínio padrão inicial "[nome de domínio].onmicrosoft.com" ou um nome de [domínio personalizado](../../active-directory/add-custom-domain.md) verificado e não federado, como "contoso.com".
    
   1. Copie ou, caso contrário, tenha em atenção à palavra-passe de utilizador gerada, para que possa dá-la ao utilizador, após a conclusão deste processo.
    
   1. Opcionalmente, pode abrir e preencher as informações em **Perfil**, **Grupos** ou **Função de diretório** para o utilizador. 
    
   1. Em **'Utilizador',** selecione **Criar**.
    
   1. Distribua de forma segura a palavra-passe gerada ao novo utilizador para que possa iniciar sessão.

1. Crie uma instância Azure AD DS. Siga as instruções nos Serviços de Domínio ativo do [Diretório Enable Azure utilizando o portal Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (a secção "Criar uma instância e configurar as definições básicas"). É importante atualizar as palavras-passe de utilizador existentes no Ative Directory para que a palavra-passe no Azure AD DS esteja sincronizada. Também é importante adicionar DNS ao Azure AD DS, como descrito em "Complete os campos na janela Básica do portal Azure para criar uma instância Azure AD DS" nessa secção.

1. Crie uma sub-rede DSVM separada na rede virtual criada na secção "Criar e configurar a rede virtual" da etapa anterior.
1. Crie uma ou mais instâncias DSVM na sub-rede DSVM.
1. Siga as [instruções](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) para adicionar o DSVM ao Diretório Ativo. 
1. Monte uma partilha de Ficheiros Azure para acolher o seu diretório de casa ou caderno para que o seu espaço de trabalho possa ser montado em qualquer máquina. (Se precisar de permissões apertadas ao nível do ficheiro, precisará do Sistema de Ficheiros de Rede [NFS] em execução em um ou mais VMs.)

   1. [Crie uma partilha de Ficheiros Azure.](../../storage/files/storage-how-to-create-file-share.md)
    
   2.  Monte esta parte no Linux DSVM. Quando selecionar **o Connect** for the Azure Files partilhar na sua conta de armazenamento no portal Azure, aparece o comando para correr na concha de bash no DSVM linux. O comando é assim:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Por exemplo, assuma que montou a sua quota de Ficheiros Azure em /data/espaço de trabalho. Agora, crie diretórios para cada um dos seus utilizadores na partilha: /data/workspace/user1, /data/workspace/user2, e assim por diante. Crie `notebooks` um diretório no espaço de trabalho de cada utilizador. 
1. Criar ligações `notebooks` `$HOME/userx/notebooks/remote`simbólicas para dentro .   

Tem agora os utilizadores no seu diretório Ativo hospedados no Azure. Ao utilizar credenciais de Diretório Ativo, os utilizadores podem iniciar sessão em qualquer DSVM (SSH ou JupyterHub) que se junte ao Azure AD DS. Como o espaço de trabalho do utilizador está numa partilha do Azure Files, os utilizadores têm acesso aos seus cadernos e outros trabalhos de qualquer DSVM quando estão a usar o JupyterHub.

Para autodimensionamento, você pode usar um conjunto de máquinavirtual para criar uma piscina de VMs que estão todos unidos ao domínio desta forma e com o disco partilhado montado. Os utilizadores podem iniciar sessão em qualquer máquina disponível no conjunto de escala de máquina virtual e ter acesso ao disco partilhado onde os seus cadernos são guardados. 

## <a name="next-steps"></a>Passos seguintes

* [Armazenar seguramente credenciais para aceder a recursos em nuvem](dsvm-secure-access-keys.md)



