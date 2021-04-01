---
title: Gerir regras de firewall - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Criar e gerir regras de firewall para Azure Database for PostgreSQL - Servidor Flexível usando o portal Azure
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8c107b88f964d8e657d6833fc3a2e8425d9053ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90936922"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Criar e gerir regras de firewall para Azure Database for PostgreSQL - Servidor Flexível usando o portal Azure

> [!IMPORTANT]
> A Azure Database for PostgreSQL - Flexible Server encontra-se atualmente em pré-visualização pública.

A azure Database for PostgreSQL - Flexible Server suporta dois tipos de métodos de conectividade de rede mutuamente exclusivos para se conectar ao seu servidor flexível. As duas opções são:

* Acesso público (endereços IP permitidos)
* Acesso privado (Integração de VNet)

Neste artigo, vamos focar-nos na criação de servidor PostgreSQL com **acesso público (endereços IP permitidos)** usando o portal Azure e forneceremos uma visão geral da gestão das regras de firewall após a criação de servidor flexível. Com *acesso público (endereços IP autorizados)*, as ligações ao servidor PostgreSQL estão restritas apenas a endereços IP permitidos. Os endereços IP do cliente devem ser permitidos nas regras de firewall. Para saber mais sobre o assunto, consulte o [acesso do Público (endereços IP autorizados)](./concepts-networking.md#public-access-allowed-ip-addresses). As regras de firewall podem ser definidas no momento da criação do servidor (recomendado), mas também podem ser adicionadas mais tarde. Neste artigo, forneceremos uma visão geral sobre como criar e gerir regras de firewall usando o acesso público (endereços IP permitidos).

## <a name="create-a-firewall-rule-when-creating-a-server"></a>Criar uma regra de firewall ao criar um servidor

1. **Selecione Criar um recurso** (+) no canto superior esquerdo do portal.
2. Selecione **Base de Dados**  >  **Azure Databases para PostgreSQL**. Também pode inserir **PostgreSQL** na caixa de pesquisa para encontrar o serviço.
3. Selecione **o servidor flexível** como opção de implementação.
4. Preencha o formulário **Básico.**
5. Vá ao **separador 'Rede'** para configurar a forma como pretende ligar-se ao seu servidor.
6. No **método conectividade,** selecione *acesso público (endereços IP autorizados)*. Para criar as **regras firewall**, especifique o nome da regra firewall e o endereço IP único, ou uma série de endereços. Se pretender limitar a regra a um único endereço IP, digite o mesmo endereço no campo para o endereço IP inicial e o endereço IP final. A abertura da firewall permite que administradores, utilizadores e aplicações acedam a qualquer base de dados no servidor PostgreSQL ao qual possuem credenciais válidas.
   > [!Note]
   > Azure Database for PostgreSQL - Flexible Server cria uma firewall ao nível do servidor. Impede que as aplicações e ferramentas externas se liguem ao servidor e a quaisquer bases de dados no mesmo, a menos que crie uma regra de firewall para abrir a firewall aos endereços IP específicos.
7. Selecione **Review + crie** para rever a configuração flexível do servidor.
8.  Selecione **Criar** para aprovisionar o servidor. O provisionamento pode levar alguns minutos.

## <a name="create-a-firewall-rule-after-server-is-created"></a>Criar uma regra de firewall após a criação do servidor

1. No [portal Azure,](https://portal.azure.com/)selecione a Base de Dados Azure para PostgreSQL - Servidor Flexível no qual pretende adicionar regras de firewall.
2. Na página do servidor flexível, em posição **De Definições,** clique em **Rede** para abrir a página de Networking para servidor flexível.

   <!--![Azure portal - click Connection Security](./media/howto-manage-firewall-portal/1-connection-security.png)-->

3. Clique **em Adicionar o endereço IP do cliente atual** nas regras de firewall. Isto cria automaticamente uma regra de firewall com o endereço IP público do seu computador, conforme percebido pelo sistema Azure.

   <!--![Azure portal - click Add My IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)-->

4. Verifique o seu endereço IP antes de guardar a configuração. Em algumas situações, o endereço IP observado pelo portal Azure difere do endereço IP utilizado no acesso aos servidores da Internet e do Azure. Portanto, poderá ter de alterar o endereço IP iniciar e end IP para que a regra funcione como esperado.

   Pode utilizar um motor de busca ou outra ferramenta on-line para verificar o seu próprio endereço IP. Por exemplo, procure "qual é o meu IP".

   <!--![Bing search for What is my IP](./media/howto-manage-firewall-portal/3-what-is-my-ip.png)-->

5. Adicione intervalos de endereços adicionais. Nas regras de firewall para a Base de Dados Azure para PostgreSQL - Servidor Flexível, pode especificar um único endereço IP ou uma série de endereços. Se pretender limitar a regra a um único endereço IP, digite o mesmo endereço no campo para o endereço IP inicial e o endereço IP final. A abertura da firewall permite que administradores, utilizadores e aplicações acedam a qualquer base de dados no servidor PostgreSQL ao qual possuem credenciais válidas.

   <!--![Azure portal - firewall rules](./media/howto-manage-firewall-portal/4-specify-addresses.png)-->

6. Clique em **Guardar** na barra de ferramentas para guardar esta regra de firewall. Aguarde a confirmação de que a atualização das regras de firewall foi bem sucedida.

   <!--![Azure portal - click Save](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)-->

## <a name="connecting-from-azure"></a>Ligar a partir do Azure

É melhor permitir que os recursos ou aplicações implementadas no Azure se conectem ao seu servidor flexível. Isto inclui aplicações web hospedadas no Azure App Service, executando em um Azure VM, um gateway de gestão de dados Azure Data Factory e muito mais. 

Quando uma aplicação dentro do Azure tenta ligar-se ao seu servidor, a firewall verifica se as ligações Azure são permitidas. Pode ativar esta definição selecionando o Acesso público do **Azure aos serviços e recursos do Azure dentro do Azure a esta** opção de servidor no portal a partir do **separador 'Rede'** e acertar **no Save**.

Os recursos não precisam de estar na mesma rede virtual (VNet) ou grupo de recursos para a regra de firewall para permitir essas ligações. Se a tentativa de ligação não for permitida, o pedido não chega à Base de Dados Azure para PostgreSQL - Servidor Flexível.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando selecionar esta opção, certifique-se de que as suas permissões de início de sessão e de utilizador limitam o acesso apenas a utilizadores autorizados.
>
> Recomendamos a escolha do **acesso privado (VNet Integration)** para aceder de forma segura ao servidor flexível.
>
## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>Gerir as regras de firewall existentes através do portal Azure

Repita os seguintes passos para gerir as regras de firewall.

- Para adicionar o computador atual, clique em + **Adicionar o endereço IP do cliente atual** nas regras de firewall. Clique em **Guardar** para guardar as alterações.
- Para adicionar endereços IP adicionais, escreva o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final. Clique em **Guardar** para guardar as alterações.
- Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique. Clique em **Guardar** para guardar as alterações.
- Para eliminar uma regra existente, clique na elipse [...] e clique em **Eliminar** para remover a regra. Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [Networking na Base de Dados Azure para PostgreSQL - Servidor Flexível](./concepts-networking.md)
- Conheça mais sobre [a Base de Dados Azure para regras de firewall pósgresQL - Flexível Servidor](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Crie e gere a base de dados Azure para regras de firewall postgreSQL utilizando O Azure CLI](./how-to-manage-firewall-cli.md).