---
title: Criar uma nova oferta de SaaS no Marketplace comercial
description: Como criar um novo Software como uma oferta de serviço (SaaS) para a listagem ou vender no Azure Marketplace, AppSource, ou através do programa de fornecedor de soluções Cloud (CSP) com o portal de Marketplace comerciais na Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 897634ac0c30b4a2cc496c225965f465a94fe4e9
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66751891"
---
# <a name="create-a-new-saas-offer"></a>Criar uma nova oferta de SaaS

Para começar a criar Software como serviço (SaaS) oferece, certifique-se de que primeiro [criar uma conta do Centro de parceiros](./create-account.md) e abra o [dashboard Marketplace comerciais](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), com o **descrição geral** separador selecionado.

![Dashboard de Marketplace comercial no Centro de parceiros](./media/new-offer-overview.png)

Selecione a + **criar um novo...** botão, em seguida, selecione o **Software como serviço** item de menu. 

Se selecionar um dos outros tipos de oferta, será redirecionado para o antigo [Cloud Partner Portal](https://cloudpartner.azure.com/).  Ofertas de SaaS apenas estão disponíveis no portal do Marketplace comercial no Centro de parceiros neste momento. 

![Criar a janela de oferta no Centro de parceiros](./media/new-offer-click.png)

O **nova oferta** é apresentada a caixa de diálogo. 

![Caixa de diálogo do novo oferta](./media/new-offer-popup.png)


## <a name="offer-id-and-name"></a>ID de oferta e o nome

- **ID da oferta**: Crie um identificador exclusivo para cada oferta na sua conta. Este ID será visível para os clientes o endereço de URL para a oferta do marketplace e modelos do Azure Resource Manager (se aplicável). ID de oferta tem de ser em minúsculas, de alfanuméricos (incluindo hífenes e carateres de sublinhado, mas sem espaço em branco). Isso está limitado a 50 carateres e não pode ser atualizado depois de selecionar criar.  
Exemplo: teste-oferta-1
<br>Resulta no URL: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Nome da oferta**: O nome oficial da sua oferta de aplicação SaaS, consistente no âmbito de publicações, anúncios e sites.  Este nome pode ser trademarked.  Oferecer o nome não pode conter espaços em branco, emojis (a menos que eles são o símbolo de marca comercial ou direitos de autor) e tem de estar limitado a 50 carateres.
<br>Exemplo: Oferta de teste 1&#8482;

Selecione **Criar**.  Uma **descrição geral da oferta** página é criada para esta oferta.  

![Descrição geral da oferta no Centro de parceiros](./media/commercial-marketplace-offer-overview.png)

## <a name="offer-overview"></a>Descrição geral da oferta

O **descrição geral da oferta** página inclui: 

- O **estado de publicação** apresenta uma representação visual das etapas necessárias para publicar esta oferta e o tempo que cada passo necessário para concluir. Ícones de passo de publicação incompleta estarão esbatidas. 

- O **descrição geral da oferta** menu contém uma lista de links para executar operações nesta oferta. Esta lista de operações irá mudar com base na seleção que fizer para a sua oferta.  
    - Se a oferta é um esboço – rascunho Delete 
    - Se a oferta está em direto – Stop vender a oferta 
    - Se a oferta está em pré-visualização – Go-live 
    - Se ainda não concluiu o início de sessão do publicador – Cancelar publicar

## <a name="offer-setup"></a>Configuração de oferta

O **oferecem a configuração** separador pede-lhe as seguintes informações. Selecione **guardar** depois de concluir estes campos.

- **Gostaria de venda através de Microsoft?** (Sim/não)
    - **Sim**, gostaria de vender a sua oferta através da Microsoft, com a Microsoft alojamento transações de marketplace em seu nome; ou 
    - **Não**, desejar simplesmente listar a sua oferta através de marketplaces, processamento de quaisquer transações monetárias independentemente da Microsoft.    

### <a name="sell-through-microsoft"></a>Venda através da Microsoft

Venda através da Microsoft fornece melhor deteção de cliente e aquisição, permite que a Microsoft para transações do marketplace de host em seu nome e tira partido das capacidades de comércio disponível globalmente da Microsoft.

#### <a name="saas-offer-requirements"></a>Requisitos de oferta de SaaS

Para listar o Software como serviço (SaaS) oferece com o Marketplace comercial no Centro de parceiros, devem ser cumpridos os seguintes critérios:

- A oferta tem de ser compatível com os clientes do Azure. (Muitas vezes, aplicações SaaS também estão alojadas no Azure para o melhor desempenho e compatibilidade, mas não se trata de um requisito.) 
- A oferta tem de utilizar [do Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) para gestão de identidades e autenticação.
- A oferta tem de utilizar [APIs de preenchimento de SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) para integrar com o Azure Marketplace.

#### <a name="billing-infrastructure-costs"></a>Custos de infraestrutura de faturação
Para ofertas de SaaS,, como o publicador, deve levar em conta as tarifas de utilização da infraestrutura do Azure e taxas de licenciamento de software como um item único custo. Esse custo é representado como uma taxa mensal fixa para o cliente. Utilização da infraestrutura do Azure é gerenciada e faturada para si, o parceiro diretamente. As tarifas de utilização da infraestrutura real não são vistas pelo cliente. Os publicadores, normalmente, optar por agrupar as tarifas de utilização da infraestrutura do Azure nos respetivos preços de licença de software. 

Software taxas de licenciamento são apresentadas como uma taxa de taxa fixa de subscrição mensal, recorrentes baseado no site e não são medidas ou baseado no consumo.

|**A custo da licença**|**US $100 por mês**|
|:---|:---|
|Custo de utilização do Azure (D1/1 núcleo)|Cobrado diretamente para o publicador, não é o cliente|
|Ser-lhe cobrado pela Microsoft|US $100,00 por mês (publicador deve levar em conta quaisquer custos de infraestrutura incorridos ou pass-through o valor da licença)|

- Neste cenário, a Microsoft cobra $100,00 para sua licença de software e paga os us $80.00 ao publicador.
- Parceiros que qualificaram para o **tarifa de serviço do Marketplace reduzido** verá uma taxa reduzida de transação no SaaS oferece desde Maio de 2019 até de 2020 de Junho. Neste cenário, a Microsoft cobra $100,00 para sua licença de software e paga os us $90.00 ao publicador.

> [!NOTE]
> **Reduzido a tarifa de serviço do Marketplace**: Para determinados o SaaS oferece que publicou no nosso Marketplace comercial, a Microsoft irá reduzir a tarifa de serviço do Marketplace de 20% (conforme descrito no contrato de publicador do Microsoft) para 10%. Por ordem para a sua oferta qualificar, pelo menos uma das suas ofertas tem foram designada pela Microsoft como estando preparado para de venda conjunta de IP ou de venda conjunta IP priorizada.  Elegibilidade cumprir, pelo menos, cinco (5) dias úteis antes do final de cada mês do calendário para receber esta tarifa de serviço do Marketplace reduzida para o mês.  A taxa de serviço do Marketplace reduzido não é aplicável a VMs, aplicações geridas ou quaisquer outros produtos disponibilizados através do nosso Marketplace comercial.  A taxa de serviço do Marketplace reduzido só estará disponível para as ofertas qualificadas para cobranças de licenças recolhidas pela Microsoft, entre 1 de Maio de 2019 e 30 de Junho de 2020.  Após esse período, a taxa de serviço do Marketplace será retornado para a quantidade de normal. 

|**Listas de Microsoft**|**US $100 por mês**|
|:---|:---|
|Microsoft presta 80% dos gastos de licenciamento <br>**Para aplicações de SaaS qualificadas, a Microsoft presta 90% do seu custo de licença*|US $80.00 por mês <br>*$* 90.00 por mês *|


#### <a name="csp-program-opt-in"></a>CSP participação no programa
O [fornecedor de soluções Cloud (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) programa permite que a ofertas de software de alcançar milhões de clientes da Microsoft qualificados com um mínimo investimento de marketing e venda.

- **Canais: Fazer a minha oferta disponível no programa CSP** (caixa de verificação)

Eleger disponibilizar a sua oferta no programa CSP permite que os fornecedores de soluções de Cloud vender seu produto como parte de uma solução integrados em aos seus clientes. 

### <a name="list-through-microsoft"></a>Lista por meio da Microsoft

Promova o seu negócio com a Microsoft através da criação de uma listagem do marketplace. Selecionar para listar a sua oferta apenas e não transact através do Microsoft significa que a Microsoft não participa diretamente em transações de licença de software. Existe uma taxa de transação associados e o publicador mantém 100% de quaisquer taxas recolhidas a partir do cliente de licenciamento de software. No entanto, o publicador é responsável por todos os aspetos da transação de licença de software, incluindo mas não limitado a de suporte: encomendar preenchimento, medição, faturação, faturação, pagamento e coleção. 

- **Como deseja que os clientes potenciais para interagir com esta oferta de listagem?**

##### <a name="get-it-now-free"></a>Obter agora (gratuito)
Liste a sua oferta aos clientes gratuitamente ao fornecer um URL válido (início com http ou https) em que eles podem aceder à sua aplicação.  Por exemplo: `https://contoso.com/saas-app`

##### <a name="free-trial"></a>Avaliação gratuita
Liste a sua oferta aos clientes numa base de avaliação gratuita, fornecendo um URL válido (início com http ou https) em que eles podem aceder à sua aplicação.  Por exemplo: `https://contoso.com/trial/saas-app`

##### <a name="contact-me"></a>Contactem-me
Recolha informações de contacto do cliente ao ligar o seu sistema de gerenciamento de relação do cliente (CRM). O cliente será solicitado para a permissão para partilhar as suas informações. Estes detalhes de cliente, juntamente com o nome da oferta, o ID e a origem de marketplace onde perceberam que a sua oferta, serão enviados para o sistema CRM que configurou. Para obter mais informações sobre como configurar o seu sistema de CRM, consulte [gestão de oportunidades potenciais do Connect](#connect-lead-management). 

## <a name="example-marketplace-offer-listing"></a>Listagem de oferta do marketplace de exemplo

![Lista com notas de ofertas do marketplace de exemplo](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Ativar uma versão de teste

Uma versão de teste é uma ótima maneira de demonstrar sua oferta aos clientes potenciais por dar-lhes a opção para "tentar antes de o comprar", resultando em maior de conversão e a geração de oportunidades potenciais altamente qualificados. [Saiba mais sobre versões de teste.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Ativar um test drive** (caixa de verificação) 

Ao ativar a versão de teste, será solicitado para configurar um ambiente de demonstração para os clientes experimentar a sua oferta para um período de tempo fixo. 

### <a name="type-of-test-drive"></a>Tipo de versão de teste

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : Um modelo de implementação que contém todos os recursos do Azure que abrangem sua solução. Produtos que se enquadram neste cenário, utilize apenas os recursos do Azure.
- **[Dynamics 365 para empresas Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft hospeda e mantém o serviço de unidade de teste (incluindo o provisionamento e a implantação) para um sistema de planeamento de recursos de empresa Business Central (Finanças, operações, cadeia de abastecimento, CRM, etc.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : A Microsoft hospeda e mantém o serviço de unidade de teste (incluindo o provisionamento e a implantação) para um sistema de envolvimento do cliente (vendas, serviço, o serviço de projeto, o serviço de campo, etc.).  
- **[Dynamics 365 para operações](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : A Microsoft hospeda e mantém o serviço de unidade de teste (incluindo o provisionamento e a implantação) para um Finanças e operações de planeamento de recursos empresariais sistema (Finanças, operações, fabrico, cadeia de fornecimento, etc.). 
- **[Aplicação lógica](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : Um modelo de implementação que abrange todas as arquiteturas de solução complexas. Nenhum produto personalizado deve utilizar este tipo de versão de teste.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : Uma ligação incorporada a um dashboard personalizados internos. Produtos que desejam demonstrar que uma interactive visual do Power BI deve utilizar este tipo de versão de teste. Tudo o que precisa para carregar aqui é o URL de BI de energia incorporados.

#### <a name="additional-test-drive-resources"></a>Recursos de unidade de teste adicionais
- [Teste de unidade de práticas recomendadas técnicos](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Versão de teste, as práticas recomendadas de Marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Testar a paginação de uma unidade de descrição geral](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Ligue-se a gestão de oportunidades potenciais

Conecte-se os clientes diretamente pela sua oferta nos marketplaces de listagem e conectar seu sistema de gerenciamento de relação do cliente (CRM), para que pode receber informações de contacto do cliente imediatamente depois de um cliente expressa interesse ou implementa sua produto.

- **Escolha um destino de oportunidades potenciais** (menu pendente): Forneça os detalhes de ligação para o sistema CRM em que gostaria de ver para enviar as oportunidades potenciais. 

Centro de parceiros suporta os seguintes sistemas CRM para gestão de oportunidades potenciais. Selecione a ligação para obter instruções de configuração.

- O Azure Blob – fornecer correio eletrónico de contacto, nome do contentor e cadeia de ligação de conta de armazenamento. 
- [Tabela do Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) – forneça o e-mail de contacto e cadeia de ligação de conta de armazenamento. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – forneça o e-mail de contacto, o URL e o modo de autenticação (Office 365 ou Azure Active Directory).
- [Ponto final de HTTPS](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) – forneça o e-mail de contacto e o URL de ponto final HTTPS. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – forneça o correio eletrónico de contacto, o ID de formulário, o ID da conta Munchkin e o ID do servidor.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) -fornecer e-mail de contacto e o ID de organização. 

#### <a name="additional-lead-management-resources"></a>Recursos de gestão de oportunidades potenciais adicionais
- [Numa gestão de perguntas frequentes](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros comuns de configuração de oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Levar um de descrição geral da gestão Pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Lembre-se **guardar** antes de passar para a secção seguinte!

## <a name="properties"></a>Propriedades
O **propriedades** separador pede-lhe definir as categorias e setores utilizados para agrupar a sua oferta no Marketplace, os contratos legais, dar suporte a sua oferta e a versão da aplicação. 

Selecione **guardar** depois de concluir estes campos. 

### <a name="category"></a>Category
Selecione um mínimo de um (1) e um máximo de três (3) categorias utilizado para agrupar a sua oferta para as áreas de pesquisa de mercado adequado. Ligue os como a oferta oferece suporte a essas categorias na descrição da oferta. 

### <a name="industry"></a>Indústria
Selecione até duas (2) os setores utilizados para agrupar a sua oferta para as áreas de pesquisa de mercado adequado. Se a sua oferta não é específica da indústria, não selecione uma. Ligue os como a oferta suporta os setores selecionados na descrição da oferta. 

### <a name="app-version"></a>Versão da aplicação
Este é um campo opcional utilizado no marketplace do AppSource para identificar o número de versão da sua oferta. 

### <a name="standard-contract"></a>Contrato Standard

- **Contrato de padrão de utilização?**

Para simplificar o processo de aprovisionamento para os clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um modelo de contrato padrão para ajudar a facilitar uma transação no marketplace. 

Em vez de criar termos e condições personalizados, os publicadores do Azure Marketplace podem optar por oferecer o respetivo software sob o contrato padrão, o que os clientes só têm de verificar e aceitar uma vez. 

O contrato padrão pode ser encontrado aqui: https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Termos de utilização

Se seus termos de licença são diferentes do contrato padrão, pode optar por introduzir os seus próprios termos legais de utilização aqui. Também pode introduzir até 10.000 carateres do texto neste campo. Se os termos de utilização exigem uma descrição mais longa, introduza uma ligação de URL única neste campo onde os seus termos de licenciamento adicionais podem ser encontrados. Esta será apresentada aos clientes como um link ativo.

Os clientes têm de aceitar estes termos antes de eles experimentam a sua aplicação. 

Lembre-se **guardar** antes de passar para a secção seguinte!

## <a name="offer-listing"></a>Listagem de oferta

A oferta listagem separador apresenta as linguagens (e mercados) em que a sua oferta está disponível, atualmente inglês (Estados Unidos) é o único local disponível. Além disso, esta página apresenta o estado da listagem específicas de idioma e a data/hora em que foi adicionado. Terá de definir os detalhes de marketplace (oferecem o nome, descrição, termos de pesquisa, etc.) para cada idioma / mercado.

### <a name="offer-listings"></a>Listas de ofertas

Forneça os detalhes que serão apresentados no marketplace, incluindo descrições da sua oferta e recursos de marketing.

- **Nome** (obrigatório): O nome definido aqui aparece como o título da sua lista de ofertas no marketplace(s) que escolheu. O nome é pré-preenchida com base na sua anterior **nova oferta** entrada.  Isso pode ser trademarked.  Isso não pode conter espaços em branco, emojis (a menos que eles são os símbolos de marcas e direitos de autor) e tem de estar limitado a 50 carateres.
- **Resumo** (obrigatório): Forneça uma breve descrição da sua oferta a ser utilizado nos resultados de pesquisa de listing(s) do marketplace. Até 100 carateres do texto podem ser introduzidas neste campo.
- **Descrição** (obrigatório): Forneça uma descrição da sua oferta a ser apresentado na descrição geral de listing(s) do marketplace. Considere incluir uma proposta de valor, os principais benefícios, todas as associações de categoria ou do setor, oportunidades de compra no aplicativo, todas as necessárias divulgações e uma ligação para saber mais.
Até 3.000 carateres do texto podem ser introduzidas neste campo. Para obter mais dicas, consulte o artigo [escrever uma descrição de grande aplicativo](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Procurar palavras-chave**: Introduza as palavras a procurar até três que os clientes podem utilizar para localizar a sua oferta no marketplace(s).
- **Instruções de introdução** (obrigatório): Explicam como configurar e começar a utilizar a sua aplicação para os clientes potenciais.  Este guia de introdução pode conter ligações para documentação online mais detalhada. Até 3.000 carateres do texto podem ser introduzidas neste campo. 

#### <a name="links"></a>Ligações

- **Política de privacidade** (obrigatório): Ligar a política de privacidade da sua organização. É responsável por garantir que a sua aplicação está em conformidade com as leis de privacidade e os regulamentos e para fornecer uma política de privacidade válido
- **Materiais de Marketing de programa CSP** (opcional): Tem de fornecer um link para materiais de marketing, se optar por expandir a sua oferta para o [fornecedor de soluções Cloud (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) programa. CSP expande a sua oferta para uma variedade maior de clientes qualificados, permitindo que os parceiros CSP agrupar, mercado e que revendem a sua oferta. Estes revendedores terá acesso aos materiais de marketing da sua oferta. Para obter mais informações, consulte [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Ligações úteis** (opcional): Documentos de online complementares opcionais sobre a sua aplicação ou serviços relacionados listados, fornecendo uma **Title** e **URL**. Adicionar links úteis adicionais clicando **+ adicionar um URL**.

#### <a name="contact-information"></a>Informações de contacto

- **Contactos**: Para cada contacto do cliente, forneça um funcionário **Name** , **número de telefone**, e **E-Mail** endereço.  (Estes *não irá* ser apresentados publicamente). R **URL de suporte** também é necessário para o **contacto de suporte** grupo.  (Esta informação *será* ser apresentados publicamente).

**Contacto de suporte** (obrigatório): Para questões de suporte geral.

**Contacto de engenharia** (obrigatório): Para questões técnicas.

**Contacte o Gestor do Channel** (obrigatório): Para questões de revendedor relacionadas com o programa CSP.

#### <a name="files-and-images"></a>Ficheiros e imagens

- **Documentos** (obrigatório): Adicione documentos relacionados de marketing da sua oferta, no formato PDF, fornecendo um mínimo de um (1) e o máximo de três (3) documentos por oferta.
- **Imagens** (opcional): Existem vários lugares onde as imagens de logótipo da sua oferta podem aparecer em todo o marketplace(s), que requerem os seguintes tamanhos – pequeno: 48x48 pixels _(obrigatório),_ médio: 90 x 90 pixels, grande: 216 x 216 pixels _(obrigatório),_ ampla: 255 x 115 pixels e Hero: 815 x 290 pixels. Todas as imagens devem estar. Formato PNG.
- **Capturas de ecrã** (obrigatório): Adicione capturas de ecrã que demonstram sua oferta. Um máximo de cinco (5) capturas de ecrã pode ser adicionado e deve ser redimensionado em pixels de 1280 x 720. Todas as imagens devem estar. Formato PNG.
- **Vídeos** (opcional): Adicione links para vídeos demonstrando a sua oferta. Pode usar links para vídeos do YouTube e/ou Vimeo, que são apresentados, juntamente com a sua oferta aos clientes. Também precisará de introduzir uma imagem em miniatura do vídeo, dimensionados para 1280 x 720 pixels no formato PNG. Pode exibir um máximo de quatro vídeos por oferta.

Lembre-se **guardar** antes de passar para a secção seguinte!

#### <a name="additional-marketplace-listing-resources"></a>Recursos de listagem do marketplace adicionais

- [As listagens de oferecer melhores práticas para o marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Pré-visualização

O **pré-visualização** separador permite-lhe definir uma limitada **público-alvo de pré-visualização** para lançar a sua oferta antes de publicar a sua oferta em direto para o público mais amplo do marketplace.

> [!IMPORTANT]
> Tem de selecionar **implemente** antes de sua oferta será publicada em direto para o público-alvo público do marketplace depois de verificar a sua oferta em pré-visualização.

- **Defina um público de pré-visualização: Adicione um único e-mail de conta do AAD/MSA por linha, juntamente com uma descrição opcional.**

Adicionar endereços de e-mail do até dez (10) manualmente ou vinte (20) se carregar um ficheiro CSV, para a conta Microsoft existente (MSA) ou contas do Azure Active Directory (AAD) para ajudar a validar a sua oferta antes da publicação em direto. Ao adicionar estas contas, está a definir um público-alvo que terá permissão acesso de pré-visualização para a sua oferta antes da sua publicação para o marketplace(s). Se a sua oferta já está em direta, pode definir um público de pré-visualização para fins de teste de quaisquer alterações ou atualizações à sua oferta.

> [!NOTE]
> O público-alvo de pré-visualização difere de um público-alvo privado. Um público de pré-visualização tem permissão para aceder à sua oferta _anteriores_ para ser publicado em direto nos mercados. Também pode optar por criar um plano e disponibilizá-lo apenas para um público-alvo privado. Na **planear listagem** separador, pode definir um público-alvo privado com o **trata de um plano privado** caixa de verificação. Em seguida, pode definir um público-alvo privado de até 20 000 clientes com IDs de inquilino do Azure.

## <a name="technical-configuration"></a>Configuração técnica

O **técnica configuração** separador define os detalhes técnicos (caminho de URL, webhook, ID de inquilino e ID da aplicação) utilizados para ligar à sua oferta. Esta ligação permite-nos aprovisionar a oferta como um recurso na subscrição do cliente do Azure, se optarem por adquiri-lo.

- **URL da página de aterrissagem** (obrigatório): Defina o site URL que os clientes que serão direcionados para entrada depois de adquirir a sua oferta do marketplace. Este URL também será o ponto final que irão receber a ligação de APIs para facilitar o comércio com a Microsoft.

- **Webhook de ligação** (obrigatório): Para todos os eventos assíncronos que a Microsoft tem de enviar a em nome do cliente (exemplo: Subscrição do Azure tornou-se inválida), é necessário fornecer um webhook da ligação. Se ainda não tiver um sistema de webhook no local, a configuração mais simples é fazer com que uma aplicação de lógica de ponto final de HTTP que irá escutar quaisquer eventos que está a ser postados nele e, em seguida, manipulá-las adequadamente (por exemplo, https:\//prod-1westus.logic.azure.com:443/work). Para obter mais informações, consulte [chamar, acionar, ou aninhar fluxos de trabalho com pontos de extremidade HTTP no logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID de inquilino do Azure AD** (obrigatório): No portal do Azure, é necessário que [criar uma aplicação do Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para que possamos validar a ligação entre os dois serviços estiver atrás de uma comunicação autenticada. Para localizar os [ID do inquilino](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id), vá para o Azure Active Directory e selecione **propriedades**, em seguida, procure o **ID de diretório** número indicado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- **ID de aplicação do Azure AD** (obrigatório): Também tem sua [ID da aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) e uma chave de autenticação. Para obter esses valores, vá para o Azure Active Directory e selecione **registos de aplicações**, em seguida, procure o **ID de aplicação** número indicado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Para localizar a chave de autenticação, aceda a **configurações** e selecione **chaves**. Terá de fornecer uma descrição e a duração e, em seguida, irá ser fornecido um valor numérico.

 Tenha em atenção que o ID da aplicação do Azure está associado ao seu ID de publicador, por isso, certifique-se de que o mesmo ID de aplicação é utilizado em todas as suas ofertas.

## <a name="plan-overview"></a>Descrição geral do plano

O **descrição geral do plano** separador permite-lhe fornecer uma variedade de opções do plano de dentro da mesma oferta. Estes planos (às vezes, denominados SKUs) poderiam ser diferente em termos de escalões de serviço, monetização ou versão. Tem de configurar pelo menos um plano para vender a sua oferta no marketplace.

Depois de criado, verá seus nomes de plano, IDs, modelos, disponibilidade (pública ou privada), de preços atual de publicação de estado e todas as ações disponíveis.

**Ações** disponíveis no **descrição geral do plano** variam consoante o estado atual do seu plano e podem incluir:

- Se o estado de plano for **rascunho** – eliminar rascunho
- Se o estado de plano for **Live** – Stop vender plano ou audiência privada de sincronização

**Criar novo plano** (mínimo de um plano para aqueles que selecionar para vender através da Microsoft)

- **ID do plano:** Crie um ID de plano exclusivo para cada plano nesta oferta. Este ID será visível para os clientes nos produtos URL e o Azure Resource Manager modelos (se aplicável). Utilize apenas carateres em minúsculas, de alfanuméricos, traços ou carateres de sublinhado. Um máximo de 50 carateres são permitidos para este ID de plano. Tenha em atenção que o ID não pode ser modificado depois de selecionar a criar.
- **Nome do plano:** Ao decidir que pretende selecionar dentro da sua oferta, os clientes Verão este nome. Crie um nome de oferta exclusiva para cada plano nesta oferta. O nome do plano é utilizado para diferenciar planos de software que podem ser uma parte da oferta do mesmo (por exemplo Nome da oferta: Windows Server; plans: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Plano de listagem

O **planear listagem** separador apresenta as linguagens (e mercados) em que o seu plano está disponível, atualmente o inglês (Estados Unidos) é o único local disponível. Além disso, esta página apresenta o estado da listagem específicas de idioma e a data/hora em que foi adicionado. Terá de definir os detalhes de marketplace (oferecem o nome, descrição, termos de pesquisa, etc.) para cada idioma / mercado.

#### <a name="plan-listing-details"></a>Plano de detalhes da lista

Selecionar um dos idiomas plano irá apresentar a **plano de listagem** informações, incluindo **nome** e **descrição.**

- **Nome**: Previamente preenchido com base na sua pré-visualização **novo plano** entrada e será apresentado como título do seu plano da oferta"Software" apresentado no marketplace.
- **Descrição:** Esta descrição é uma oportunidade para explicar o que torna este plano de software exclusivo e quaisquer diferenças de outros planos de software na sua oferta. Pode conter até 500 carateres.

Selecione **guardar** depois de concluir estes campos.

#### <a name="plan-pricing-and-availability"></a>Plano de preços e a disponibilidade

O **preços e disponibilidade** separador permite-lhe configurar os mercados em que este plano estará disponível, o modelo de monetização pretendido, o preço e o período de faturação. Além disso, pode indicar se pretende tornar o plano visíveis para todos os utilizadores ou apenas para clientes específicos (um público privado).

#### <a name="markets"></a>Mercados

- **Editar mercados** (opcional)

Cada plano tem de estar disponível no mercado de pelo menos um. Selecione a caixa de verificação para qualquer localização de mercado em que pretende disponibilizar este plano. Uma caixa de pesquisa e um botão de seleção "Imposto Remitted" países, em que a Microsoft remits vendas e imposto sobre o uso em seu nome, são incluídos para ajudar. 


Se já tiver configurado os preços para o seu plano nos Estados Unidos dólares (USD) e adicionar outra localização de mercado, o preço para o novo mercado será calculado de acordo com as taxas de câmbio atuais. Deve sempre ser revisto o preço para cada mercado antes da publicação. Preços podem ser revistos utilizando o link "Exportar os preços (. xlsx)" depois de guardar as alterações.

#### <a name="pricing"></a>Preços

- **Modelo de preços**: Tarifa fixa ou posto de trabalho com base em

**Tarifa fixa:** Ative o acesso a sua oferta com um preço de taxa fixa de preço mensal ou anual individual. Isso é por vezes referido como preços baseados no site.

**Posto de trabalho com base em:** Permitir o acesso à sua oferta com o preço com base no número de utilizadores a aceder a oferta ou a ocupar *postos de trabalho*. Este modelo baseado em posto de trabalho permite-lhe definir o mínimo e máximo de estações permitidas baseado no preço. Dessa forma, os pontos de preço diferente podem ser configurados com base no número de utilizadores através da configuração de vários esquemas.  Estes campos são opcionais. Se deixado em branco, o número de utilizadores será interpretado como não tendo um limite (mínimo de 1) e máx. de quantas pode suportar o sistema. Estes campos podem ser editados como parte de uma atualização ao seu plano.

Depois de publicado, a opção de modelo de preços faturação não pode ser alterada. Além disso, todos os planos para a oferta mesmo têm de partilhar o mesmo modelo de preços.

- **Período de faturação**: Mensal ou anual

Selecione a frequência com que os clientes têm de pagar o preço listado. Preço, pelo menos, um mensal ou anual tem de ser fornecido, ou ambas as opções podem ser disponibilizadas aos clientes.

- **Preço**: USD por mês ou USD por ano

Os preços conjunto na moeda local (USD = dólar norte-americano) são convertidas em moeda local do selecionados de todos os mercados com as taxas de câmbio atuais disponíveis durante a configuração. Valide estes preços antes de publicar, exportar a folha de cálculo de preços e rever o preço em cada mercado. Se gostaria de definir os preços personalizados num mercado individual, modificar e importar a folha de cálculo do preço. São responsáveis por validar estes preços e proprietário estas definições.
**Tem de guardar as alterações de preços para ativar a exportação de dados de preços.*

Reveja os preços cuidadosamente antes de publicar, como há algumas restrições sobre o que pode alterar-se após a publicação de um plano:

- Assim que for publicado um plano, não é possível alterar o modelo de preços.
- Depois de um período de faturação está publicado para um plano, não é possível remover mais tarde.
- Assim que for publicado um preço para um mercado no seu plano, não é possível alterar mais tarde.

### <a name="plan-audience"></a>Planear o público-alvo

Tem a opção de configurar cada plano para ser visível para todos os utilizadores ou apenas um público-alvo específico à sua escolha. Pode atribuir a associação a esse público-alvo restrito com IDs de inquilino do Azure AD.

#### <a name="privacy"></a>Privacidade

- **Este é um plano privado** (caixa de seleção opcional)

Marque esta caixa para tornar o seu plano privada e visíveis apenas para o público-alvo restrito à sua escolha. Depois de publicado como um plano privado, pode atualizar o público-alvo ou optar por disponibilizar o plano para todos os utilizadores. Depois de um plano for publicado como visível para todos os utilizadores, tem de permanecer visível para todos os utilizadores. (O plano não pode ser configurado como um plano privado novamente).

- **Público restrito (IDs de inquilino)**

Atribua o público-alvo que têm acesso a este plano privado. É atribuído acesso utilizando os IDs de inquilino com a opção para incluir uma descrição de cada ID de inquilino atribuído. Pode ser adicionado um máximo de IDs de inquilino 10 ou 20 000 clientes IDs de inquilino se importar um ficheiro de folha de cálculo. csv.

Um inquilino é uma representação de uma organização, com um ID representada como um GUID (Globally Unique Identifier, um número inteiro de 128 bits usado para identificar recursos). É uma instância dedicada do Azure AD que uma organização ou um programador de aplicações recebe quando a organização ou o programador de aplicações cria uma relação com o Microsoft – como inscrever-se no Azure, no Microsoft Intune ou no Microsoft 365. Cada inquilino do Azure AD é distinto e separado dos outros inquilinos do Azure AD. Para verificar o inquilino, inicie sessão no portal do Azure com a conta que pretende utilizar para gerir a sua aplicação. Se tiver um inquilino, irá ter automaticamente sessão iniciada no mesmo e verá o nome do inquilino imediatamente por baixo do nome da sua conta. Se passar com o rato por cima do nome da sua conta, no canto superior direito do portal do Azure, verá o seu nome, e-mail, diretório e ID de inquilino (um GUID) e o seu domínio. Se a sua conta estiver associada a vários inquilinos, pode selecionar o nome da sua conta para abrir um menu onde pode alternar entre inquilinos. Cada inquilino tem o seu próprio ID de inquilino. Também pode ver ID do inquilino da sua organização através de um URL de nome de domínio em: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

Enquanto as ofertas de SaaS utilizam IDs de inquilino para definir um público-alvo privado, outros tipos de oferta podem utilizar os IDs de subscrição do Azure (que também são representados como GUIDs).

> [!NOTE]
> O público-alvo privada (ou o público-alvo restrito) é diferente de um público de pré-visualização. Na **[pré-visualização](#preview)** guia, pode definir um público de pré-visualização. Um público de pré-visualização tem permissão para aceder à sua oferta *anteriores* para a oferta que está a ser publicada em direto no marketplace. Embora a designação de público-alvo privada só se aplica a um plano específico, o público-alvo de pré-visualização pode ver todos os planos (privada ou não), mas somente durante o período de pré-visualização limitada, enquanto o plano é testado e validado.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Lista de exemplo dos planos dentro de uma oferta do marketplace

![Planeamento de mercado de exemplo listagem com notas](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Versão de teste

O **Test drive do** separador permite-lhe configurar uma demonstração (ou "unidade de teste") que permite aos clientes experimentar a sua oferta antes de consolidar comprá-la. Saiba mais no artigo [o que é o Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Se já não pretende fornecer uma versão de teste para a sua oferta, regresse à **[oferecem o programa de configuração](#offer-setup)** página e desmarque **ativar test-drive**.

### <a name="technical-configuration"></a>Configuração técnica
Os seguintes tipos de unidades de teste estão disponíveis, cada um com seus próprios requisitos de configuração técnica.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplicação lógica](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configuração da técnica não obrigatória)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Versão de teste de configuração técnica para o Azure Resource Manager

Um modelo de implementação que contém todos os recursos do Azure que abrangem sua solução. Produtos que se enquadram neste cenário, utilize apenas os recursos do Azure. Saiba mais sobre como configurar uma [test-drive do Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiões** (obrigatório): Atualmente, existem 26 regiões suportados pelo Azure onde sua versão de teste pode ficar disponível. Normalmente, desejará fazer sua versão de teste disponíveis nas regiões em que prevê o maior número de clientes, para que podem selecionar a região mais próxima para o melhor desempenho. Terá de certificar-se de que a sua subscrição tem permissão para implementar todos os recursos necessários em cada uma das regiões do que se está a selecionar.

- **Instâncias**: Selecione o tipo (frequente ou frio) e número de instâncias disponíveis, que irão ser multiplicadas pelo número de regiões onde a sua oferta está disponível.

**Frequente**: Este tipo de instância é implementado e espera acesso por região selecionada. Os clientes podem aceder de forma instantânea *frequente* instâncias de uma versão de teste, em vez de ter de esperar por uma implementação. A desvantagem é que estas instâncias são sempre em execução na sua subscrição do Azure, para que eles incorre num tempo de atividade maior custos. É altamente recomendável ter, pelo menos, um *frequente* instâncias, como a maioria dos clientes não quiser esperar para implementações completas, resultando numa entrega na utilização do cliente, se não *frequente* instância está disponível.

**Frio**: Este tipo de instância representa o número total de instâncias que possivelmente pode ser implementado por região. Instâncias frios requerem o modelo de Gestor de recursos de unidade de teste todo implementar quando um cliente solicitar a versão de teste, então *frio* instâncias são muito carreguem mais lentamente do que *frequente* instâncias. A desvantagem é que só precisa de pagar a duração da unidade de teste, é *não* sempre em execução na sua subscrição do Azure como com um *frequente* instância.

- **Modelo do Azure Resource Manager de teste de unidade**: Carregue o. zip que contém o modelo do Azure Resource Manager.  Saiba mais sobre como criar um modelo Azure Resource Manager no artigo guia de introdução [criar e implementar modelos Azure Resource Manager com o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duração do test drive** (obrigatório): Introduza o período de tempo que o Test-Drive permanecerá ativo, no n. º de horas. O Test-Drive automaticamente termina após o final deste período de tempo. Esta duração pode apenas apostar conjunto por um número inteiro de horas (por exemplo horas de "2", "1.5" não é válido).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Versão de teste de configuração técnica para o Dynamics 365

A Microsoft pode remover a complexidade da configuração de uma versão de teste ao alojamento e manter o serviço de aprovisionamento e a implantação usando esse tipo de versão de teste. A configuração para este tipo de versão de teste alojada é o mesmo, independentemente se o test-drive se destina a um público-alvo Business Central, envolvimento do cliente ou operações.

- **Número máximo de unidades de teste em simultâneo** (obrigatório): Defina o número máximo de clientes que pode utilizar a sua versão de teste de uma só vez. Cada utilizador em simultâneo irá consumir uma licença do Dynamics 365, enquanto o test-drive está ativo, por isso terá de se certificar de que tem licenças suficientes suportar o limite máximo definido. Valor recomendado de 3 a 5.

- **Duração do test drive** (obrigatório): Introduza o período de tempo que o Test-Drive permanecerá ativo ao definir o número de horas. Depois de tantas horas, a sessão irá terminar e já não está a consumir uma das suas licenças. Recomendamos um valor de 2 a 24 horas, consoante a complexidade da sua oferta. Esta duração pode apenas apostar conjunto por um número inteiro de horas (por exemplo horas de "2", "1.5" não é válido).  O utilizador pode pedir uma nova sessão se ficar sem tempo e deseja acessar a unidade de teste novamente.

- **URL de instância** (obrigatório): O URL em que o cliente começará a unidade de teste. Normalmente, o URL da sua instância do Dynamics 365 executando seu aplicativo com dados de exemplo instalados (por exemplo, https://testdrive.crm.dynamics.com).

- **URL da API Web de instâncias** (obrigatório): Obter o URL da API Web para a sua instância do Dynamics 365 iniciando sessão na sua conta do Microsoft 365 e navegar até **configurações** \&gt; **Personalização** \&gt; **Recursos para desenvolvedores** \&gt; **Instância Web API (URL de raiz do serviço)** , copie o URL encontrado aqui (por exemplo, https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Nome da função** (obrigatório): Forneça o nome de função de segurança que definiu na sua versão de teste personalizado do Dynamics 365. Será atribuída ao utilizador durante a sua versão de teste (por exemplo, test-drive-função).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuração técnica para a versão de teste de aplicação lógica

Nenhum produto personalizado deve utilizar este tipo de modelo de implementação de unidade de teste que abrange uma variedade de arquiteturas de soluções complexas. Para obter mais informações sobre como configurar a aplicação lógica de unidades de teste, visite [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no GitHub.

- **Região** (lista de necessária, a única seleção suspensa): Atualmente, existem 26 regiões suportados pelo Azure onde sua versão de teste pode ficar disponível. Os recursos para a aplicação lógica serão implantados na região que selecionou. Se a sua aplicação lógica tiver todos os recursos personalizados armazenados numa região específica, certificar-se de que essa região está selecionada aqui. A melhor forma de fazer isso é totalmente implementar a sua aplicação lógica localmente na sua subscrição do Azure no portal e certifique-se de que funciona corretamente antes de efetuar esta seleção.

- **Número máximo de unidades de teste em simultâneo** (obrigatório): Defina o número máximo de clientes que pode utilizar a sua versão de teste de uma só vez. Estas unidades já estão implementadas, permitindo aos clientes instantaneamente acessá-los sem esperar por uma implementação de teste.

- **Duração do test drive** (obrigatório): Introduza o período de tempo que o Test-Drive permanecerá ativo, no n. º de horas. O test-drive automaticamente termina após o final deste período de tempo.

- **Nome do grupo de recursos do Azure** (obrigatório): Introduza o [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nome onde sua versão de teste da aplicação lógica é guardado.

- **Nome da aplicação lógica do Azure** (obrigatório): Introduza o nome da aplicação lógica que atribui a unidade de teste para o usuário. Esta aplicação lógica tem de ser guardada no grupo de recursos do Azure acima.

- **Nome da aplicação lógica desaprovisionamento** (obrigatório): Introduza o nome da aplicação lógica que desprovisiona a versão de teste, assim que o cliente estiver concluído. Esta aplicação lógica tem de ser guardada no grupo de recursos do Azure acima.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuração técnica não é necessária para versões de teste do Power BI

Produtos que desejam demonstrar que um interactive visual do Power BI pode utilizar uma ligação incorporada para partilhar um dashboard personalizados internos como sua versão de teste, não são necessárias mais técnica configuração necessária. Saiba mais sobre como configurar[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) aplicações de modelo.

### <a name="deployment-subscription-details"></a>Detalhes da subscrição de implementação

Para poder implementar a versão de teste em seu nome, criar e fornecer uma subscrição do Azure separados, únicos. (Não é necessário para as unidades de teste do Power BI).

- **ID de subscrição do Azure** (necessário para o Azure Resource Manager e o Logic apps): Introduza o ID de subscrição para conceder acesso aos seus serviços de conta do Azure para utilização de recursos de relatórios e de faturação. Recomendamos que considere [criar uma subscrição do Azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) a utilizar para versões de teste se ainda não tiver um. Pode encontrar o seu ID de subscrição do Azure ao iniciar sessão para o [portal do Azure](https://portal.azure.com/) e ao navegar para o **subscrições** separador do menu do lado esquerdo. Selecione a guia irá apresentar o seu ID de subscrição (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID de inquilino do Azure AD** (obrigatório): Introduza o seu Azure Active Directory (AD) [ID de inquilino](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id). Para localizar este ID, inicie sessão no [portal do Azure](https://portal.azure.com/), selecione o separador de diretório Active Directory no menu da esquerda, selecione **propriedades** , em seguida, procure o **ID de diretório** número indicado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Também pode ver ID do inquilino da sua organização utilizar o seu URL de nome de domínio em: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

- **Nome de inquilino do Azure AD** (necessário para o Dynamics 365): Introduza o nome do Azure Active Directory (AD). Para localizar este nome, inicie sessão para o [portal do Azure](https://portal.azure.com/), no canto superior direito, o nome do inquilino será listado sob o nome da sua conta.

- **ID de aplicação do Azure AD** (obrigatório): Introduza o seu Azure Active Directory (AD) [ID da aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). Para localizar este ID, inicie sessão na [portal do Azure](https://portal.azure.com/), selecione o separador de diretório Active Directory no menu da esquerda, selecione **registos das aplicações**, em seguida, procure o **ID da aplicação** número listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- **Chave da aplicação do Azure AD** (obrigatório): Introduza o seu Azure Active Directory (AD) [chave da aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). Para localizar este ID, inicie sessão para o [portal do Azure](https://portal.azure.com/), selecione o separador de diretório Active Directory no menu da esquerda, selecione **registos das aplicações** , em seguida, selecione **definições**  >  **Chaves**.

Lembre-se **guardar** antes de passar para a secção seguinte!

### <a name="test-drive-listings-optional"></a>Listagens de unidade de teste (opcional)

O **listagens de Test-Drive** opção localizada sob o **Test drive do** separador apresenta as linguagens (e mercados) em que a sua versão de teste está disponível, atualmente o inglês (Estados Unidos) é o único local disponível . Além disso, esta página apresenta o estado da listagem específicas de idioma e a data/hora em que foi adicionado. Terá de definir os detalhes de unidade de teste (descrição, manual do usuário, vídeos, etc.) para cada idioma/mercado.

- **Descrição** (obrigatório): Descreva a sua versão de teste, o que irá ser demonstrado, os objetivos para o utilizador para experimentar, recursos para explorar e quaisquer informações relevantes para ajudar o utilizador, determinar se deve adquirir a sua oferta. Até 3.000 carateres do texto podem ser introduzidas neste campo. 

- **Aceder às informações** (necessário para o Azure Resource Manager e a lógica de unidades de teste): Explique o que um cliente precisar de saber para poder aceder e utilizar esta versão de teste. Examinar um cenário para utilizar a sua oferta e exatamente o que o cliente deve saber para aceder às funcionalidades em toda a unidade de teste. Até 10.000 carateres do texto podem ser introduzidas neste campo.

- **Manual do usuário** (obrigatório): Uma passo a passo detalhada de sua experiência de unidade de teste. O Manual do usuário deve abranger exatamente o que pretende que o cliente para obter de ter a versão de teste e servir como uma referência para as perguntas que podem ter. O ficheiro tem de estar no formato PDF e ter o nome (máximo de 255 carateres) depois de carregar.

- **Vídeos: Adicionar vídeos** (opcional): Vídeos podem ser carregados para o YouTube ou Vimeo e referenciados aqui com uma imagem de ligação e a miniatura (533 x 324 pixels), para que um cliente possa visualizar uma passagem de informações para ajudá-los a compreender melhor o test-drive, incluindo como utilizar com êxito os recursos do seu oferecem e a compreender cenários que realçam os seus benefícios.
  - **Nome** (obrigatório)
  - **URL (YouTube ou Vimeo apenas)** (obrigatório)
  - **Miniatura (533 x 324px)** : Ficheiro de imagem tem de estar no formato PNG.

Selecione **guardar** depois de concluir estes campos.

## <a name="publish"></a>Publicar

#### <a name="submit-offer-to-preview"></a>Submeter oferecem para pré-visualizar

Depois de concluir todas as seções necessárias da oferta, selecione **publicar** no canto superior direito do portal. Será novamente direcionado para o **revisão e publicar** página. 

Se esta for a primeira vez que esta oferta de publicação, pode:

- Ver o estado de conclusão para cada secção da oferta.
    - *Não foi iniciada* – significa que a seção não foi tocada e precisa ser concluída.
    - *Incompleta* – significa que a seção tem erros de que precisam ser corrigidos ou requer mais informações sobre como ser fornecido. . Voltar para as secções e atualizá-lo.
    - *Completa* – significa que a seção estiver concluída, todos os dados necessários foi fornecido e não existirem erros. Todas as seções da oferta tem de estar no estado de conclusão antes de pode enviar a oferta.
- Fornecem instruções de testes para a equipe de certificação para se certificar de que a sua aplicação é testada corretamente, além de quaisquer notas suplementares útil para compreender a sua aplicação.
- Submeter a oferta para publicação selecionando **submeter**. Nós lhe enviaremos um e-mail a informá-lo quando uma versão de pré-visualização da oferta está disponível para rever e aprovar. Tem de regressar ao centro de parceiros e selecionar **Go-live** para a oferta publicar a sua oferta para o público (ou se oferece uma privada, para o público-alvo privado).

## <a name="next-steps"></a>Passos Seguintes

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
