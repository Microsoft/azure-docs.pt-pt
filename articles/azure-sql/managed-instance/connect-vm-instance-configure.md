---
title: Configure conectividade VM Azure
titleSuffix: Azure SQL Managed Instance
description: Ligue-se a uma instância gerida azure SQL utilizando o Estúdio de Gestão de Servidores SQL a partir de uma máquina virtual Azure.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: b94993a67fadad2c31c8787deab49e62dd457512
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044375"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-managed-instance"></a>Quickstart: Configure Azure VM para ligar a uma instância gerida pelo Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este quickstart mostra-lhe como configurar uma máquina virtual Azure para se ligar a uma Instância Gerida Azure SQL utilizando o SQL Server Management Studio (SSMS). 


Para um início rápido que mostre como se conectar a partir de um computador cliente no local usando uma ligação ponto-a-local, consulte [Configurar uma ligação ponto-a-local](point-to-site-p2s-configure.md)

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart utiliza os recursos criados na [Create a SQL Managed Instance](instance-create-quickstart.md) como ponto de partida.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-new-subnet-vnet"></a>Criar uma nova vnet de sub-rede

Os passos seguintes criam uma nova subrede no SQL Managed Instance VNet para que uma máquina virtual Azure possa ligar-se à Instância Gerida sQL. A subnet SQL Managed Instance é dedicada a Instâncias Geridas SQL. Não se pode criar outros recursos, como máquinas virtuais Azure, naquela sub-rede.

1. Abra o grupo de recursos para a Instância Gerida SQL que criou no Arranque rápido da [Create a SQL Managed Instance.](instance-create-quickstart.md) Selecione a rede virtual para a sua Instância Gerida SQL.

   ![Recursos de instância gerida sQL](./media/connect-vm-instance-configure/resources.png)

2. Selecione **Subnets** e, em seguida, selecione **+ Subnet** para criar uma nova sub-rede.

   ![Subredes de instância gerida SQL](./media/connect-vm-instance-configure/subnets.png)

3. Preencha o formulário utilizando as informações desta tabela:

   | Definição| Valor sugerido | Descrição |
   | ---------------- | ----------------- | ----------- |
   | **Nome** | Qualquer nome válido|Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming).|
   | **Intervalo de endereços (bloco CIDR)** | Uma gama válida | O valor padrão é bom para este arranque rápido.|
   | **Grupo de segurança da rede** | Nenhum | O valor padrão é bom para este arranque rápido.|
   | **Tabela de rota** | Nenhum | O valor padrão é bom para este arranque rápido.|
   | **Pontos finais de serviço** | 0 selecionados | O valor padrão é bom para este arranque rápido.|
   | **Delegação de sub-rede** | Nenhum | O valor padrão é bom para este arranque rápido.|

   ![Nova subnet SQL Managed Instance para vm cliente](./media/connect-vm-instance-configure/new-subnet.png)

4. Selecione **OK** para criar esta sub-rede adicional no SQL Managed Instance VNet.

## <a name="create-vm-in-new-subnet"></a>Criar VM em nova subrede 

Os seguintes passos mostram-lhe como criar uma máquina virtual na nova subnet para ligar à Instância Gerida SQL.

## <a name="prepare-the-azure-virtual-machine"></a>Prepare a máquina virtual Azure

Uma vez que a SQL Managed Instance é colocada na sua rede virtual privada, você precisa criar um Azure VM com uma ferramenta de cliente SQL instalada, como O Estúdio de Gestão de Servidores SQL ou Azure Data Studio. Esta ferramenta permite ligar-se à Instância Gerida SQL e executar consultas. Este início rápido utiliza o SQL Server Management Studio.

A maneira mais fácil de criar uma máquina virtual cliente com todas as ferramentas necessárias é usar os modelos do Gestor de Recursos Azure.

1. Certifique-se de que está inscrito no portal Azure em outro separador de navegador. Em seguida, selecione o seguinte botão para criar uma máquina virtual cliente e instale o Estúdio de Gestão de Servidores SQL:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Preencha o formulário utilizando as informações no quadro seguinte:

   | Definição| Valor sugerido | Descrição |
   | ---------------- | ----------------- | ----------- |
   | **Subscrição** | Uma subscrição válida | Deve ser uma subscrição na qual tem permissão para criar novos recursos. |
   | **Grupo de Recursos** |O grupo de recursos que especificou no Início rápido da [Create SQL Managed Instance.](instance-create-quickstart.md)|Este grupo de recursos deve ser aquele em que existe o VNet.|
   | **Localização** | A localização do grupo de recursos | Este valor é povoado com base no grupo de recursos selecionado. |
   | **Nome da máquina virtual**  | Qualquer nome válido | Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming).|
   |**Nome de utilizador de administrador**|Qualquer nome de utilizador válido|Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). Não utilize "serveradmin" pois é uma função reservada ao nível do servidor.<br>Utiliza este nome de utilizador sempre que [se conecta ao VM](#connect-to-virtual-machine).|
   |**Palavra-passe**|Qualquer palavra-passe válida|A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Utiliza esta palavra-passe sempre que [se conecta ao VM](#connect-to-virtual-machine).|
   | **Tamanho da máquina virtual** | Qualquer tamanho válido | O padrão neste modelo de **Standard_B2s** é suficiente para este arranque rápido. |
   | **Localização**|[resourceGroup().localização].| Não mude este valor. |
   | **Nome da rede virtual**|A rede virtual em que criou o SQL Managed Instance.|
   | **Nome da sub-rede**|O nome da sub-rede que criou no procedimento anterior| Não escolha a sub-rede em que criou a Instância Gerida SQL.|
   | **Localização de artefactos** | [implementação().properties.templateLink.uri] | Não mude este valor. |
   | **artefactos Localização Sas token** | deixar em branco | Não mude este valor. |

   ![criar a VM cliente](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   Se usou o nome VNet sugerido e a subnet padrão na [criação da sua Instância Gerida SQL,](instance-create-quickstart.md)não precisa de alterar os dois parâmetros. Caso contrário, deverá alterar estes valores para os valores que inseriu quando configura o ambiente de rede.

3. Selecione o **I concordo com os termos e condições indicados acima da** caixa de verificação.
4. Selecione **Comprar** para implantar o Azure VM na sua rede.
5. Selecione o ícone **Notificações** para ver o estado de implantação.

> [!IMPORTANT]
> Não continue até aproximadamente 15 minutos após a criação da máquina virtual para dar tempo aos scripts pós-criação para instalar o Estúdio de Gestão de Servidores SQL.

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Os passos seguintes mostram como ligar à máquina virtual recentemente criada através de uma ligação de ambiente de trabalho remoto.

1. Após a conclusão da implementação, vá para o recurso de máquina virtual.

    ![VM](./media/connect-vm-instance-configure/vm.png)  

2. Selecione **Ligar**.

   Um formulário de protocolo de ambiente de trabalho remoto (ficheiro.rdp) aparece com o endereço IP público e o número de porta para a máquina virtual.

   ![Formulário RDP](./media/connect-vm-instance-configure/rdp.png)  

3. Selecione **Download RDP File**.

   > [!NOTE]
   > Também pode utilizar o SSH para se ligar ao seu VM.

4. Feche o **Formulário de Ligação à forma** da máquina virtual.
5. Para ligar à sua VM, abra o ficheiro RDP transferido.
6. Quando solicitado, selecione **Connect**. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) a partir da Mac App Store.

7. Introduza o nome de utilizador e a palavra-passe especificado ao criar a máquina virtual e, em seguida, escolha **OK**.

8. Poderá receber um aviso de certificado durante o processo de início de sessão. Escolha **Sim** ou **Continue** a prosseguir com a ligação.

Está ligado à sua máquina virtual no painel do Gestor de Servidores.

## <a name="connect-to-sql-managed-instance"></a>Ligar à Instância Gerida do SQL 

1. Na máquina virtual, abra o Estúdio de Gestão de Servidores SQL (SSMS).

   Demora alguns momentos a abrir, pois precisa de completar a sua configuração, uma vez que esta é a primeira vez que o SSMS é iniciado.
2. Na caixa de diálogo **Connect to Server,** introduza o nome de **anfitrião** totalmente qualificado para a sua Instância Gerida SQL na caixa de **nome** sql. Selecione A autenticação do **servidor SQL,** forneça o seu nome de utilizador e palavra-passe e, em seguida, selecione **Connect**.

    ![ligação SSMS](./media/connect-vm-instance-configure/ssms-connect.png)  

Depois de ligar, pode ver as bases de dados do sistema e dos utilizadores no nó de bases de dados e vários objetos em nós de Segurança, Objetos de Servidor, Replicação, Gestão, SQL Server Agent e Gerador de Perfis de XEvent.

## <a name="next-steps"></a>Próximos passos

- Para um início rápido que mostre como se conectar a partir de um computador cliente no local utilizando uma ligação ponto-a-local, consulte [Configurar uma ligação ponto-a-local](point-to-site-p2s-configure.md).
- Para uma visão geral das opções de ligação para aplicações, consulte [Connect as suas aplicações para SQL Managed Instance](connect-application-instance.md).
- Para restaurar uma base de dados existente do SQL Server a partir do local para uma instância gerida pela SQL, pode utilizar o Serviço de Migração de Bases de [Dados Azure (DMS) para migração](../../dms/tutorial-sql-server-to-managed-instance.md) ou o [comando RESTABELECIMENTO T-SQL](restore-sample-database-quickstart.md) para restaurar a partir de um ficheiro de backup de base de dados.
