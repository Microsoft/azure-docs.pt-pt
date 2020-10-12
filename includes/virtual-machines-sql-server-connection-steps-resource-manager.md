---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 4e79fef08af8ff73ce63ab4732c9efd77e3a5d3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184214"
---
### <a name="configure-a-dns-label-for-the-public-ip-address"></a>Configurar uma Etiqueta de DNS para o endereço IP público

Para ligar ao Motor de Base de Dados do SQL Server a partir da Internet, considere criar uma Etiqueta de DNS para o seu endereço IP público. Pode ligar-se por endereço IP, mas a Etiqueta de DNS cria Um Registo que é mais fácil identificar e deduz o endereço IP público subjacente.

> [!NOTE]
> As Etiquetas de DNS não são necessárias se planear ligar apenas a instância do SQL Server dentro da mesma Virtual Network ou apenas localmente.

Para criar uma Etiqueta de DNS, comece por selecionar **Máquinas virtuais** no portal. Selecione a sua VM do SQL Server para ver as respetivas propriedades.

1. No descrição geral da máquina virtual, selecione o **Endereço IP público**.

    ![endereço IP público.](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

1. Nas propriedades do Endereço IP público, expanda **Configuração**.

1. Introduza um nome para a Etiqueta de DNS. Este nome é um Registo A que pode ser utilizado para ligar à sua VM do SQL Server diretamente pelo nome em vez de ser pelo Endereço IP.

1. Clique no botão **Guardar**.

    ![etiqueta de DNS](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Ligar ao Motor da Base de Dados a partir de outro computador

1. Num computador ligado à Internet, abra o SQL Server Management Studio (SSMS). Se não tiver o SQL Server Management Studio, poderá transferi-lo [aqui](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

1. Na caixa de diálogo **Ligar ao Servidor** ou **Ligar ao Motor de Base de Dados**, edite o valor **Nome do servidor**. Introduza o endereço IP ou o nome DNS completo da máquina virtual (determinado na tarefa anterior). Também pode adicionar uma vírgula e fornecer a porta TCP do SQL Server. Por exemplo, `mysqlvmlabel.eastus.cloudapp.azure.com,1433`.

1. Na caixa **Autenticação**, selecione **Autenticação do SQL Server**.

1. Na caixa **Início de sessão**, escreva o nome de um início de sessão SQL válido.

1. Na caixa **Palavra-passe**, escreva a palavra-passe do início de sessão.

1. Clique em **Ligar**.

    ![ligação SSMS](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)