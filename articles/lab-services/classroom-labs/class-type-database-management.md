---
title: Criar um laboratório para ensinar gestão de bases de dados para bases de dados relacionais Microsoft Docs
description: Aprenda a criar um laboratório para ensinar a gestão de bases de dados relacionais.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 4c375487b30595251753021033c98cf0ca1e8dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469923"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Criar um laboratório para ensinar gestão de bases de dados para bases de dados relacionais

Este artigo descreve como criar um laboratório para uma aula básica de gestão de bases de dados em Serviços de Laboratório Azure. Os conceitos de bases de dados são um dos cursos introdutórios ministrados na maioria dos departamentos de Ciências da Computação na faculdade. A Linguagem De Consulta Estruturada (SQL) é um padrão internacional. O SQL é o idioma padrão para a gestão da base de dados de relações, incluindo a adição, acesso e gestão de conteúdos numa base de dados.  É mais notável pelo seu processamento rápido, fiabilidade comprovada, facilidade e flexibilidade de utilização.

Neste artigo, vamos mostrar como configurar um modelo de máquina virtual num laboratório com o MySQL Database Server e o servidor SQL Server 2019.  [MySQL](https://www.mysql.com/) é um sistema de gestão de bases de dados relacional de código aberto (RDBMS) de código aberto disponível livremente.  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) é a versão mais recente do RDBMS da Microsoft.

## <a name="lab-configuration"></a>Configuração do laboratório

Para montar este laboratório, precisa de uma subscrição azure e uma conta de laboratório para começar. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que conseguir uma subscrição do Azure, pode criar uma nova conta de laboratório nos Serviços de Laboratório Azure. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [tutorial para configurar uma conta de laboratório.](tutorial-setup-lab-account.md)  Também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações da conta de laboratório

Ative as definições descritas na tabela abaixo para a conta de laboratório. Para obter mais informações sobre como ativar imagens de marketplace, consulte [as imagens do Mercado de Especificar disponíveis para criadores de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Definição de conta de laboratório | Instruções |
| ------------------- | ------------ |
|Imagem de mercado| Ative a imagem 'SQL Server 2019 Standard on Windows Server 2019' para utilização na sua conta de laboratório.|

### <a name="lab-settings"></a>Configurações de laboratório

Utilize as definições na tabela abaixo ao configurar um laboratório de sala de aula.  Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [a criação de um tutorial](tutorial-setup-classroom-lab.md)de laboratório de sala de aula.

| Configurações de laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da máquina virtual| Média. Este tamanho é mais adequado para bases de dados relacionais, caches na memória e análise.|
|Imagem de máquina virtual| Padrão do SQL Server 2019 no Windows Server 2019|

## <a name="template-machine-configuration"></a>Configuração da máquina de modelo

Para instalar o MySQL no Windows Server 2019, pode seguir os passos mencionados no [Servidor Comunitário De Instalação e Execução MySQL numa máquina virtual](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

O SQL Server 2019 está pré-instalado na imagem da máquina virtual que escolhemos na criação do novo laboratório.

## <a name="cost-estimate"></a>Estimativa de custos

Vamos cobrir uma possível estimativa de custos para esta classe.  Usaremos uma turma de 25 alunos.  Há 20 horas de horário de aulas programado.  Além disso, cada aluno recebe uma quota de 10 horas para trabalhos de casa ou tarefas fora da hora de aula programada.  O tamanho da máquina virtual que escolhemos era médio, que é de 42 unidades de laboratório.

Aqui está um exemplo de uma possível estimativa de custos para esta classe:

25 \* alunos (20 horas programadas \* + 10 horas de quota) 0,42 USD por hora = 315,00 USD

Mais detalhes sobre os preços, consulte o [Preço dos Serviços do Laboratório Azure.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Conclusão

Este artigo acompanhou-o através dos passos necessários para criar um laboratório para conceitos básicos de gestão de bases de dados usando tanto o MySQL como o SQL Server. Pode utilizar uma configuração semelhante para outras bases de dados.

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns à criação de qualquer laboratório.

- [Criar e gerir um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)
