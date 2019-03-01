---
title: MySQL que aloja os servidores no Azure Stack | Documentos da Microsoft
description: Como adicionar instâncias do MySQL para o aprovisionamento através do fornecedor de recursos de adaptador de MySQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: eb5d1ca55efed7e83739f21a432b61b6aad25f70
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194015"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Adicionar servidores de alojamento para o fornecedor de recursos do MySQL

Pode alojar uma MySQL que aloja a instância de servidor numa máquina virtual (VM) no [do Azure Stack](azure-stack-poc.md), ou numa VM fora do seu ambiente do Azure Stack, desde que o fornecedor de recursos do MySQL, pode ligar à instância.

> [!NOTE]
> O fornecedor de recursos do MySQL deve ser criado na subscrição de fornecedor padrão enquanto devem ser criados os servidores de hospedagem do MySQL em faturáveis, subscrições de utilizador. O servidor do fornecedor de recursos não deve ser utilizados para alojar bases de dados do utilizador.

Versões do MySQL 5.6, 5.7 e 8.0 podem ser utilizadas para os servidores de alojamento. A RP MySQL não suporta a autenticação de caching_sha2_password; que será adicionado na próxima versão. Servidores MySQL 8.0 devem ser configurados para utilizar mysql_native_password. MariaDB também é suportado.

## <a name="connect-to-a-mysql-hosting-server"></a>Ligar a um servidor de hospedagem do MySQL

Certificar-se de que tem as credenciais para uma conta com privilégios de administrador de sistema. Para adicionar um servidor de hospedagem, siga estes passos:

1. Inicie sessão no portal de operador do Azure Stack como um administrador de serviço.
2. Selecione **Todos os serviços**.
3. Sob o **recursos administrativos** selecionar categoria **servidores de hospedagem do MySQL** > **+ adicionar**. Esta ação abre o **adicionar um servidor de hospedagem do MySQL** caixa de diálogo, mostrada na captura de ecrã seguinte.

   ![Configurar um servidor de hospedagem](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Forneça os detalhes de ligação da sua instância do servidor MySQL.

   * Para **nome do servidor que aloja MySQL**, forneça o nome de domínio completamente qualificado (FQDN) ou um endereço IPv4 válido. Não utilize o nome abreviado de VM.
   - O administrador predefinido **nome de utilizador** para o Bitnami MySQL imagens disponíveis no mercado do Azure Stack é *raiz*. 
   - Se não souber a raiz **palavra-passe**, consulte a [Bitnami documentação](https://docs.bitnami.com/azure/faq/#how-to-find-application-credentials) para saber como fazê-lo. 
   - Uma instância de MySQL predefinida não é fornecida, pelo que tem de especificar o **tamanho de servidor de alojamento de em GB**. Introduza um tamanho próximo da capacidade do servidor da base de dados.
   * Manter a predefinição para **subscrição**.
   * Para **grupo de recursos**, crie um novo ou utilizar um grupo existente.

   > [!NOTE]
   > Se a instância do MySQL pode ser acessada por inquilino e o administrador do Azure Resource Manager, pode colocá-lo sob o controle do fornecedor de recursos. No entanto, a instância do MySQL **tem** ser alocada exclusivamente para o fornecedor de recursos.

5. Selecione **SKUs** para abrir o **SKU criar** caixa de diálogo.

   ![Criação de um SKU de MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   O SKU **nome** deve refletir as propriedades do SKU para que os utilizadores podem implementar seus bancos de dados para o SKU adequado.

6. Selecione **OK** para criar o SKU.
> [!NOTE]
> SKUs podem demorar até uma hora para ser visível no portal. Não é possível criar uma base de dados até que o SKU é implementada e executada.

7. Sob **adicionar um servidor de hospedagem do MySQL**, selecione **criar**.

À medida que adiciona servidores, atribuí-las para um novo ou existente SKU para diferenciar as ofertas de serviço. Por exemplo, pode ter uma instância de enterprise MySQL que fornece a base de dados aumentada e de cópias de segurança automáticas. Pode reservar este servidor de alto desempenho para diferentes departamentos da sua organização.

## <a name="security-considerations-for-mysql"></a>Considerações de segurança para o MySQL

As seguintes informações aplicam-se para a RP e o MySQL alojar servidores:

* Certifique-se de que todos os servidores de hospedagem estão configurados para a comunicação utilizando TLS 1.2. Ver [configuração do MySQL para utilizar ligações encriptadas](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html).
* Empregar [encriptação de dados transparente](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html).
* A RP MySQL não suporta a autenticação de caching_sha2_password.

## <a name="increase-backend-database-capacity"></a>Aumentar a capacidade de base de dados de back-end

Pode aumentar a capacidade de base de dados de back-end ao implementar mais servidores MySQL no portal do Azure Stack. Adicione estes servidores para um SKU de novo ou existente. Se adicionar um servidor para um SKU existente, certifique-se de que as características de servidor são os mesmos que os outros servidores no SKU.

## <a name="sku-notes"></a>Notas SKU
Utilize um nome SKU que descreve as capacidades dos servidores no SKU, como a capacidade e desempenho. O nome serve como um auxílio para ajudar os utilizadores a implementar as bases de dados para o SKU adequado. Por exemplo, pode utilizar nomes SKU para diferenciar as ofertas de serviço, as seguintes características:
  
* alta capacidade
* elevado desempenho
* elevada disponibilidade

Como melhor prática, todos os servidores de hospedagem num SKU devem ter as mesmas características de desempenho e de recursos.

SKUs não podem ser atribuídos a utilizadores ou grupos específicos.

SKUs podem demorar até uma hora para ser visível no portal. Os utilizadores não é possível criar uma base de dados até que o SKU estiver totalmente criado.

Para editar um SKU, aceda a **todos os serviços** > **MySQL adaptador** > **SKUs**. Selecione o SKU para modificar, faça as alterações necessárias e clique em **guardar** para guardar as alterações. Para eliminar um SKU que não é mais necessária, aceda a **todos os serviços** > **MySQL adaptador** > **SKUs**. O nome SKU com o botão direito e selecione **eliminar** eliminá-lo.

> [!TIP]
> Pode editar ou eliminar as quotas de fornecedor de recursos MySQL na mesma localização.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Tornar os servidores de base de dados MySQL disponíveis para os seus utilizadores

Crie planos e ofertas para que os servidores de base de dados MySQL disponíveis para os utilizadores. Adicionar o serviço de Microsoft.MySqlAdapter ao plano e criar uma quota de novo. MySQL não permite a limitava o tamanho dos bancos de dados.

## <a name="next-steps"></a>Passos Seguintes

[Criar uma base de dados MySQL](azure-stack-mysql-resource-provider-databases.md)