---
title: Grupo de Confiança do Servidor
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre o Server Trust Group e como gerir a confiança entre as Instâncias Geridas Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: 911d7ffa2b1d313147ca73d0ceb285ea2e84b1f7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979627"
---
# <a name="use-server-trust-groups-to-setup-and-manage-trust-between-sql-managed-instances"></a>Use grupos de confiança do servidor para configurar e gerir a confiança entre as instâncias geridas do SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Server Trust Group é um conceito usado para gerir a confiança entre Azure SQL Managed Instances. Ao criar um grupo e adicionar-lhe instâncias de confiança baseadas em certificados é estabelecido entre todos os membros e isso pode ser usado para diferentes cenários de instância cruzada. Remover servidores do grupo ou eliminar o grupo resulta na remoção da confiança entre os servidores. Para criar ou eliminar o utilizador do Grupo Server Trust precisa de ter permissões de escrita em Casos Geridos.
[O Server Trust Group](https://aka.ms/mi-server-trust-group-arm) é um objeto Azure Resource Manager que corresponde à entidade do portal Azure chamada **SQL trust group**.

> [!NOTE]
> O Server Trust Group é introduzido na pré-visualização pública de transações distribuídas entre Azure SQL Managed Instances e atualmente tem algumas limitações que serão descritas mais tarde neste artigo.

## <a name="server-trust-group-setup"></a>Configuração do Grupo De Confiança do Servidor

A seguinte secção descreve a configuração do Grupo De Confiança do Servidor.

1. Aceda ao [portal do Azure](https://portal.azure.com/).

2. Navegue para Azure SQL Managed Instance que planeia adicionar a um grupo de confiança do Servidor recém-criado.

3. Nas definições **de Segurança,** selecione o separador **grupos de confiança SQL.**

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="Grupos de confiança do servidor":::

4. Na página de configuração do Grupo Server Trust selecione o ícone **do Novo Grupo.**

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="Grupos de confiança do servidor":::

5. No **grupo fiduciário SQL** crie lâmina para definir o **nome do Grupo**. Tem de ser único em todas as regiões onde residem os membros do grupo. **O âmbito** de confiança define o tipo de cenário de cross-instance que é ativado com o grupo de confiança Server e na pré-visualização o único âmbito de confiança aplicável são **as transações distribuídas,** pelo que é pré-selecionado e não pode ser alterado. Todos os **membros do Grupo** devem pertencer à mesma **subscrição,** mas podem estar em diferentes grupos de recursos. Selecione o **grupo de Recursos** e o **SQL Server/instância** para escolher a Instância Gerida Azure SQL que será membro do grupo.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="Grupos de confiança do servidor":::

6. Depois de todos os campos necessários serem povoados, clique em **Guardar**.

## <a name="server-trust-group-maintenance-and-deletion"></a>Manutenção e eliminação do Grupo Server Trust

Não há como editar o grupo de confiança do Servidor. Para remover uma Instância Gerida de um grupo, é necessário eliminar o grupo e criar um novo.

A secção seguinte descreve o processo de eliminação do grupo de confiança do Servidor. 
1. Aceda ao portal do Azure.
2. Navegue para uma Instância Gerida que pertence ao grupo fiduciário.
3. Nas definições **de Segurança** selecione o **separador grupos de confiança SQL.**
4. Selecione o grupo de confiança que pretende eliminar.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="Grupos de confiança do servidor":::
5. Clique **em Eliminar Grupo**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="Grupos de confiança do servidor":::
6. Digite o nome do Grupo Server Trust para confirmar a eliminação e clique em **Eliminar**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="Grupos de confiança do servidor":::

> [!NOTE]
> A eliminação do Grupo De Confiança do Servidor pode não remover imediatamente a confiança entre as duas Instâncias Geridas. A remoção de confiança pode ser aplicada invocando uma [falha](https://docs.microsoft.com/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) de Instâncias Geridas. Consulte os [problemas conhecidos](https://docs.microsoft.com/azure/azure-sql/database/doc-changes-updates-release-notes?tabs=managed-instance#known-issues) para obter as últimas atualizações sobre este assunto.

## <a name="limitations"></a>Limitações

Durante a pré-visualização pública, as seguintes limitações aplicam-se aos Grupos de Confiança do Servidor.
 * O nome do Grupo Server Trust deve ser único em todas as regiões onde os seus membros estão.
 * O grupo pode conter apenas instâncias geridas Azure SQL e devem estar sob a mesma Assinatura Azure.
 * O grupo pode ter exatamente duas instâncias geridas. Se precisar de executar transações distribuídas em mais de duas Instâncias Geridas que podem ser feitas criando o Server Trust Group para cada par de Instâncias Geridas.
 * As transações distribuídas são o único âmbito aplicável para os Grupos de Confiança do Servidor.
 * O Server Trust Group só pode ser gerido a partir do portal Azure. O suporte powerShell e CLI virá mais tarde.
 * O Server Trust Group não pode ser editado no portal Azure. Só pode ser criado ou largado.
 * As limitações adicionais das transações distribuídas podem estar relacionadas com o seu cenário. O mais notável é que as instâncias geridas sql que participam no Server Trust Group devem ser alcançáveis através de pontos finais privados, ou em outra conectividade deve funcionar no nível de rede virtual. Certifique-se de que está ciente das [atuais limitações de transações distribuídas para Azure SQL Managed Instance](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview#limitations).

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre transações distribuídas em Azure SQL Managed Instance, consulte [transações distribuídas.](../database/elastic-transactions-overview.md)
* Para obter atualizações de lançamento e problemas conhecidos, consulte [as notas de lançamento de Casos Geridos](../database/doc-changes-updates-release-notes.md).
* Para pedidos de funcionalidades, adicione-os ao [fórum De Instância Gerida](https://feedback.azure.com/forums/915676-sql-managed-instance).