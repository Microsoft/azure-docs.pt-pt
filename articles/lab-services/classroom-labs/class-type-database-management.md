---
title: Configurar um laboratório para ensinar o gerenciamento de banco de dados para bancos de dados relacionais | Microsoft Docs
description: Saiba como configurar um laboratório para ensinar o gerenciamento de bancos de dados relacionais.
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
ms.openlocfilehash: 92152652ca264cace12d3f780ebec0f80bb2048a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233769"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Configurar um laboratório para ensinar o gerenciamento de bancos de dados relacionais

Este artigo descreve como configurar um laboratório para uma classe de gerenciamento de bancos de dados básico no Azure Lab Services. Os conceitos de bancos de dados são um dos cursos introdutórios ensinados na maioria dos departamentos de ciência da computação na faculdade. O linguagem SQL (SQL) é um padrão internacional. O SQL é a linguagem padrão para gerenciamento de banco de dados de relações, incluindo a adição, o acesso e o gerenciamento de conteúdo em um banco de dados.  Ele é mais observado para seu processamento rápido, confiabilidade comprovada, facilidade e flexibilidade de uso.

Neste artigo, mostraremos como configurar um modelo de máquina virtual em um laboratório com o servidor de banco de dados MySQL e o servidor SQL Server 2019.  O [MySQL](https://www.mysql.com/) é um RDBMS (sistema de gerenciamento de banco de dados relacional) de software livre disponível gratuitamente.  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) é a versão mais recente do RDBMS da Microsoft.

## <a name="lab-configuration"></a>Configuração do laboratório

Para configurar este laboratório, você precisa de uma assinatura do Azure e uma conta de laboratório para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você poderá criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre como criar uma nova conta de laboratório, consulte [tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).  Você também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações de conta do laboratório

Habilite as configurações descritas na tabela a seguir para a conta do laboratório. Para obter mais informações sobre como habilitar imagens do Marketplace, consulte [especificar imagens do Marketplace disponíveis para criadores de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#specify-marketplace-images-available-to-lab-creators).

| Configuração de conta do laboratório | Instruções |
| ------------------- | ------------ |
|Imagem do Marketplace| Habilite a imagem ' SQL Server 2019 Standard no Windows Server 2019 ' para uso em sua conta de laboratório.|

### <a name="lab-settings"></a>Configurações do laboratório

Use as configurações na tabela abaixo ao configurar um laboratório de sala de aula.  Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [configurar um tutorial de laboratório de sala de aula](tutorial-setup-classroom-lab.md).

| Configurações do laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da máquina virtual| Média. Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise.|
|Imagem da máquina virtual| SQL Server 2019 Standard no Windows Server 2019|

## <a name="template-machine-configuration"></a>Configuração da máquina de modelo

Para instalar o MySQL no Windows Server 2019, você pode seguir as etapas mencionadas em [instalar e executar o MySQL Community Server em uma máquina virtual](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

SQL Server 2019 é pré-instalado na imagem de máquina virtual que escolhemos ao criar o novo laboratório.

## <a name="cost-estimate"></a>Estimativa dos custos

Vamos abordar uma possível estimativa de custo para essa classe.  Usaremos uma classe de 25 alunos.  Há 20 horas de tempo de classe agendada.  Além disso, cada aluno recebe uma cota de 10 horas de casa ou atribuições fora do tempo de classe agendado.  O tamanho da máquina virtual que escolhemos era médio, que é 42 unidades de laboratório.

Aqui está um exemplo de uma possível estimativa de custo para esta classe:

25 alunos \* (20 horas agendadas + 10 horas de cota) \* US $0,42 USD por hora = 315, 0 USD

Mais detalhes sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão

Este artigo descreveu as etapas necessárias para criar um laboratório para conceitos básicos de gerenciamento de banco de dados usando o MySQL e o SQL Server. Você pode usar uma configuração semelhante para outras classes de bancos de dados.

## <a name="next-steps"></a>Passos Seguintes

As próximas etapas são comuns à configuração de qualquer laboratório.

- [Criar e gerenciar um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users)
