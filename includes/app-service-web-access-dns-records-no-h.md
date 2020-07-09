---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 66d3397fae24ee2546dae4eb5d7c9d188f9ede99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78944139"
---
> [!NOTE]
> Pode utilizar o Azure DNS para configurar um nome DNS personalizado para o Azure App Service. Para obter mais informações, veja [Utilizar o DNS do Azure para oferecer definições de domínio personalizado para um serviço do Azure](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

Inicie sessão no site do fornecedor do seu domínio.

Localize a página para gerir os registos DNS. Cada fornecedor de domínio tem a sua própria interface de registos DNS, por isso, consulte a documentação do fornecedor. Procure áreas do site com os nomes **Nome de Domínio**, **DNS** ou **Gestão de Servidor de Nomes**. 

Em muitos casos, pode encontrar a página de registos DNS ao procurar uma ligação como **Os meus domínios** nas informações da sua conta. Aceda a essa página e procure uma ligação que tenha um nome parecido com **Ficheiro de zona**, **Registos DNS** ou **Configuração avançada**.

A captura de ecrã seguinte mostra um exemplo de uma página de registos DNS:

![Página de registos DNS de exemplo](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

Na captura de ecrã de exemplo, selecione **adicionar** para criar um registo. Alguns fornecedores têm ligações diferentes para adicionar diferentes tipos de registos. Novamente, consulte a documentação do fornecedor.

> [!NOTE]
> Para alguns fornecedores, como a GoDaddy, as alterações aos registos DNS só entram em vigor quando selecionar uma ligação **Guardar Alterações** separada. 
