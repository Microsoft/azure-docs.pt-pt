---
title: Getting Started - Microsoft Threat Modeling Tool - Azure [ Microsoft Docs
description: Esta é uma visão geral mais profunda que destaca a Ferramenta de Modelação de Ameaças em ação.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 1454826095bcced9b20935405c0befd5a1ed1ddd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68728204"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Começando com a ferramenta de modelação de ameaças

A Microsoft Threat Modeling Tool 2018 foi lançada como GA em setembro de 2018 como um **[clique gratuito para descarregar](https://aka.ms/threatmodelingtool)**. A alteração do mecanismo de entrega permite-nos empurrar as últimas melhorias e correções de bugs para os clientes sempre que abrem a ferramenta, facilitando a manutenção e utilização.
Este artigo leva-o através do processo de começar com a abordagem de modelação de ameaças Microsoft SDL e mostra-lhe como usar a ferramenta para desenvolver grandes modelos de ameaça como espinha dorsal do seu processo de segurança.

Este artigo baseia-se no conhecimento existente da abordagem de modelação de ameaças SDL. Para uma revisão rápida, consulte aplicações web de **[Modelação de Ameaças](https://msdn.microsoft.com/library/ms978516.aspx)** e uma versão arquivada de Falhas de Segurança Uncover Usando o artigo da STRIDE **[Approach](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** MSDN publicado em 2006.

Para resumir rapidamente, a abordagem envolve criar um diagrama, identificar ameaças, atenuá-las e validar cada mitigação. Aqui está um diagrama que realça este processo:

![Processo SDL](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Iniciar o processo de modelação de ameaças

Quando lançar a Ferramenta de Modelação de Ameaças, notará algumas coisas, como se pode ver na imagem:

![Página de início em branco](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Secção modelo de ameaça

| Componente                                   | Detalhes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Botão de Feedback, Sugestões e Problemas** | Leva-te o **[Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** para todas as coisas SDL. Dá-lhe a oportunidade de ler o que outros utilizadores estão a fazer, juntamente com salções e recomendações. Se ainda não consegue encontrar o que procura, envie um e-mail tmtextsupport@microsoft.com para a nossa equipa de apoio para o ajudar.                                                                                                                            |
| **Criar um Modelo**                          | Abre uma tela em branco para desenhar o seu diagrama. Certifique-se de selecionar qual modelo gostaria de usar para o seu modelo                                                                                                                                                                                                                                                                                                                                                                       |
| **Modelo para novos modelos**                 | Deve selecionar qual modelo usar antes de criar um modelo. O nosso modelo principal é o modelo de modelo de ameaça Azure, que contém stencils, ameaças e mitigações específicos do Azure. Para modelos genéricos, selecione a Base de Conhecimento SDL TM do menu suspenso. Quer criar o seu próprio modelo ou submeter um novo para todos os utilizadores? Confira a nossa página gitHub **[repositório de modelos](https://github.com/Microsoft/threat-modeling-templates)** para saber mais                              |
| **Abra um Modelo**                            | <p>Abre modelos de ameaça previamente guardados. A funcionalidade Models Recentemente Aberta saem bem se precisar de abrir os seus ficheiros mais recentes. Quando pairar sobre a seleção, encontrará 2 formas de abrir modelos:</p><p><ul><li>Open From this Computer – forma clássica de abrir um ficheiro utilizando o armazenamento local</li><li>Aberto a partir do OneDrive – as equipas podem usar pastas no OneDrive para salvar e partilhar todos os seus modelos de ameaça num único local para ajudar a aumentar a produtividade e a colaboração</li></ul></p> |
| **Guia de início**                   | Abre a página principal da **[Ferramenta de Modelação de Ameaças](threat-modeling-tool.md)** da Microsoft                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Secção do modelo

| Componente               | Detalhes                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Criar novo modelo** | Abre um modelo em branco para você construir. A menos que tenha um vasto conhecimento em modelos de construção do zero, recomendamos que construa a partir dos existentes |
| **Modelo aberto**       | Abre os modelos existentes para que faça alterações                                                                                                              |

A equipa de Ferramentas de Modelação de Ameaças está constantemente a trabalhar para melhorar a funcionalidade e experiência da ferramenta. Algumas pequenas alterações podem ocorrer ao longo do ano, mas todas as grandes mudanças requerem reescritas no guia. Consulte-o muitas vezes para garantir que obtém os mais recentes anúncios.

## <a name="building-a-model"></a>Construção de um modelo

Nesta secção, seguimos:

- Cristina (uma desenvolvedora)
- Ricardo (gestor de programas) e
- Ashish (um teste)

Estão a passar pelo processo de desenvolvimento do seu primeiro modelo de ameaça.

> Olá Cristina, trabalhei no diagrama do modelo de ameaça e quis ter a certeza de que tínhamos os detalhes certos. Pode ajudar-me a ver?
> Cristina: Absolutamente. Vamos dar uma vista de olhos.
> Ricardo abre a ferramenta e partilha o ecrã com Cristina.

![Modelo de ameaça básica](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Ok, parece simples, mas podes passar por isso?
> Ricardo: Claro! Aqui está a avaria:
> - O nosso utilizador humano é desenhado como uma entidade externa - um quadrado
> - Estão a enviar comandos para o nosso servidor Web, o círculo
> - O servidor Web está a consultar uma base de dados (duas linhas paralelas)

O que ricardo acabou de mostrar Cristina é um DFD, abreviatura de Diagrama de Fluxo de **[Dados.](https://en.wikipedia.org/wiki/Data_flow_diagram)** A Ferramenta de Modelação de Ameaças permite que os utilizadores especifiquem os limites de confiança, indicados pelas linhas pontilhadas vermelhas, para mostrar onde diferentes entidades estão no controlo. Por exemplo, os administradores de TI requerem um sistema de Diretório Ativo para fins de autenticação, pelo que o Diretório Ativo está fora do seu controlo.

> Cristina: Parece-me bem. E as ameaças?
> Deixe-me mostrar-lhe.

## <a name="analyzing-threats"></a>Analisar ameaças

Uma vez clicado na visão de análise da seleção do menu de ícones (ficheiro com lupa), é levado para uma lista de ameaças geradas a Ferramenta de Modelação de Ameaças encontrada com base no modelo padrão, que utiliza a abordagem SDL chamada **[STRIDE (Spoofing, Tampering, Info Disclosure, Repúdio, Negação de Serviço e Elevação de Privilégios)](https://en.wikipedia.org/wiki/STRIDE_(security))**. A ideia é que o software esteja sob um conjunto previsível de ameaças, que podem ser encontradas usando estas 6 categorias.

Esta abordagem é como proteger a sua casa garantindo que cada porta e janela tem um mecanismo de bloqueio no lugar antes de adicionar um sistema de alarme ou perseguir o ladrão.

![Ameaças Básicas](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo começa por selecionar o primeiro item da lista. Eis o que acontece:

Primeiro, a interação entre os dois stencils é melhorada

![Interação](./media/threat-modeling-tool-getting-started/interaction.png)

Segundo, informações adicionais sobre a ameaça aparecem na janela Ameaças Propriedades

![Informação de Interação](./media/threat-modeling-tool-getting-started/interactioninfo.png)

A ameaça gerada ajuda-o a compreender potenciais falhas de design. A categorização stride dá-lhe uma ideia sobre potenciais vetores de ataque, enquanto a descrição adicional diz-lhe exatamente o que está errado, juntamente com potenciais formas de mitigar. Ele pode usar campos editáveis para escrever notas nos detalhes da justificação ou alterar classificações prioritárias dependendo da barra de bugs da sua organização.

Os modelos azure têm detalhes adicionais para ajudar os utilizadores a entender não só o que está errado, mas também como corrigi-lo adicionando descrições, exemplos e hiperligações à documentação específica do Azure.

A descrição fê-lo perceber a importância de adicionar um mecanismo de autenticação para evitar que os utilizadores fossem falsificados, revelando a primeira ameaça a ser trabalhada. A poucos minutos da discussão com Cristina, entenderam a importância de implementar o controlo de acessos e os papéis. Ricardo preencheu algumas notas rápidas para garantir que estas fossem implementadas.

À medida que Ricardo entrava nas ameaças no âmbito da Divulgação de Informação, percebeu que o plano de controlo de acesso exigia algumas contas de leitura apenas para a auditoria e geração de relatórios. Questionou-se se esta deveria ser uma nova ameaça, mas as mitigações eram as mesmas, pelo que observou a ameaça em conformidade.
Ele também pensou um pouco mais na divulgação de informação e percebeu que as fitas de reserva iam precisar de encriptação, um trabalho para a equipa de operações.

As ameaças não aplicáveis ao desenho devido a atenuações ou garantias de segurança existentes podem ser alteradas para "Not Aplicável" a partir da queda do Status. Existem três outras opções: Não Iniciada – seleção por defeito, Investigação de Necessidades – usada para acompanhar itens e mitigada – uma vez que está totalmente trabalhada.

## <a name="reports--sharing"></a>Relatórios & partilha

Assim que Ricardo passa pela lista com Cristina e acrescenta notas importantes, mitigações/justificações, mudanças prioritárias e de estado, ele seleciona Relatórios -> Criar Relatório Completo -> Relatório de Poupança, que imprime um bom relatório para ele passar com os colegas para garantir que o trabalho de segurança adequado é implementado.

![Informação de Interação](./media/threat-modeling-tool-feature-overview/report.png)

Se ricardo quiser partilhar o ficheiro, pode facilmente fazê-lo economizando na conta OneDrive da sua organização. Assim que o fizer, pode copiar o link do documento e partilhá-lo com os seus colegas. 

## <a name="threat-modeling-meetings"></a>Reuniões de modelação de ameaças

Quando Ricardo enviou o seu modelo de ameaça ao seu colega usando o OneDrive, Ashish, o tester, ficou subjugado. Parecia que Ricardo e Cristina perderam alguns casos importantes de canto, que poderiam ser facilmente comprometidos. O seu ceticismo é um complemento a modelos de ameaça.

Neste cenário, depois de Ashish ter assumido o modelo de ameaça, convocou duas reuniões de modelação de ameaças: uma reunião para sincronizar o processo e percorrer os diagramas e, em seguida, uma segunda reunião para revisão de ameaças e aprovação.

No primeiro encontro, Ashish passou 10 minutos a passear toda a gente através do processo de modelação de ameaças SDL. Em seguida, puxou o diagrama do modelo de ameaça e começou a explicá-lo em detalhe. Em cinco minutos, um importante componente desaparecido tinha sido identificado.

Poucos minutos depois, Ashish e Ricardo entraram numa discussão alargada sobre como o servidor web foi construído. Não era a forma ideal de uma reunião prosseguir, mas todos acabaram por concordar que descobrir a discrepância mais cedo lhes ia poupar tempo no futuro.

No segundo encontro, a equipa passou pelas ameaças, discutiu algumas formas de as abordar e assinou o modelo de ameaça. Verificaram o documento no controlo de fontes e continuaram com o desenvolvimento.

## <a name="thinking-about-assets"></a>Pensar em ativos

Alguns leitores que têm modelo de ameaça podem notar que não falamos de bens. Descobrimos que muitos engenheiros de software entendem melhor o seu software do que compreendem o conceito de ativos e quais os ativos em que um intruso pode estar interessado.

Se vais ameaçar modelo de uma casa, podes começar por pensar na tua família, fotos insubstituíveis ou obras de arte valiosas. Talvez possa começar por pensar em quem pode entrar e no sistema de segurança atual. Ou pode começar por considerar as características físicas, como a piscina ou a varanda da frente. Estes são análogos a pensar sobre ativos, atacantes ou design de software. Qualquer uma destas três abordagens funciona.

A abordagem à modelação de ameaças que apresentámos aqui é substancialmente mais simples do que a microsoft fez no passado. Descobrimos que a abordagem de design de software funciona bem para muitas equipas. Esperamos que inclua o seu.

## <a name="next-steps"></a>Passos Seguintes

Envie as suas perguntas, comentários tmtextsupport@microsoft.come preocupações para . **[Descarregue](https://aka.ms/threatmodelingtool)** a Ferramenta de Modelação de Ameaças para começar.
