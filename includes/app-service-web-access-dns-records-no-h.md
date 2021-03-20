---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 2f490a5b12484a91e963d068810b292d7761521a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95986561"
---
> [!NOTE]
> Pode utilizar o Azure DNS para configurar um nome DNS personalizado para o Azure App Service. Para obter mais informações, veja [Utilizar o DNS do Azure para oferecer definições de domínio personalizado para um serviço do Azure](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

1. Inicie sessão no site do fornecedor do seu domínio.

1. Localize a página para gerir os registos DNS. Cada fornecedor de domínio tem a sua própria interface de registos DNS, por isso, consulte a documentação do fornecedor. Procure áreas do site com os nomes **Nome de Domínio**, **DNS** ou **Gestão de Servidor de Nomes**.

   Frequentemente, pode encontrar a página de registos DNS visualizando as informações da sua conta e, em seguida, procurando um link como **os meus domínios**. Vá a essa página e procure um link que tenha o nome de algo como **Zone file**, **DNS Records** ou **configuração Avançada**.

   A captura de ecrã seguinte mostra um exemplo de uma página de registos DNS:

   ![Screenshot que mostra uma página de registos DNS exemplo.](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Na imagem de exemplo, **selecione Adicionar** para criar um registo. Alguns fornecedores têm ligações diferentes para adicionar diferentes tipos de registos. Novamente, consulte a documentação do fornecedor.

> [!NOTE]
> Para alguns fornecedores, como a GoDaddy, as alterações aos registos DNS só entram em vigor quando selecionar uma ligação **Guardar Alterações** separada.
