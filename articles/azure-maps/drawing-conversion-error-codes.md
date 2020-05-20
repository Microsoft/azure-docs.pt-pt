---
title: Erros e avisos de conversão de gráficos do Azure Maps
description: Saiba mais sobre os erros de Conversão e avisos que poderá encontrar durante a utilização do serviço de conversão Do Mapa Azure. Leia as recomendações sobre como resolver os erros e os avisos, com alguns exemplos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: d79c42f3bdf84efcdf2187741ac270087be05272
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681990"
---
# <a name="drawing-conversion-errors-and-warnings"></a>Erros de conversão de desenho e avisos

O serviço de [conversão Do Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) permite converter pacotes de desenho carregados em dados de mapas. Os pacotes de desenho devem respeitar os requisitos do [pacote de desenho.](drawing-requirements.md) Se um ou mais requisitos não forem cumpridos, o serviço de Conversão devolverá erros ou avisos. Este artigo lista os códigos de erro de conversão e de aviso, com recomendações sobre como resolvê-los. Também fornece alguns exemplos de desenhos que podem fazer com que o serviço de Conversão devolva estes códigos.

O serviço de conversão terá sucesso se houver avisos de conversão. No entanto, recomenda-se que reveja e resolva todos os avisos. Um aviso significa que parte da conversão foi ignorada ou fixada automaticamente. A não resolução dos avisos pode resultar em erros nos últimos processos.

## <a name="general-warnings"></a>Avisos Gerais

### <a name="geometrywarning"></a>**geometriaAviso**

#### <a name="description-for-geometrywarning"></a>*Descrição para geometriaAviso*

Um **geometriaOOO** ocorre quando o desenho contém uma entidade inválida. Uma entidade inválida é uma entidade que não se conforma com constrangimentos geométricos. Exemplos de uma entidade inválida são um polígono auto-intersecto ou um PolyLine não fechado numa camada que apenas suporta geometria fechada.

O serviço de Conversão é incapaz de criar uma funcionalidade de mapa a partir de uma entidade inválida e, em vez disso, ignora-o.

#### <a name="examples-for-geometrywarning"></a>*Exemplos de geometriaAviso*

* As duas imagens abaixo mostram exemplos de polígonos auto-interseccionais.

     ![Exemplo de um polígono auto-intersecto](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![Exemplo de um polígono auto-intersecto](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* Abaixo está uma imagem que mostra um PolyLine não fechado. Assuma que a camada apenas suporta geometria fechada.

    ![Exemplo de uma PolyLine não fechada](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*Como corrigir geometriaAviso*

Inspecione a **geometriaAviso** para que cada entidade verifique se segue constrangimentos geométricos.

### <a name="unexpectedgeometryinlayer"></a>**inesperadaGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*Descrição para inesperadaGeometryInLayer*

Um aviso **inesperado GeometryInLayer** ocorre quando o desenho contém geometria que é incompatível com o tipo de geometria esperado para uma determinada camada. Quando o serviço de conversão devolve um aviso **inesperado GeometryInLayer,** ignorará essa geometria.

#### <a name="example-for-unexpectedgeometryinlayer"></a>*Exemplo para inesperadaGeometryInLayer*

A imagem abaixo mostra um PolyLine não fechado. Assuma que a camada apenas suporta geometria fechada.

![Exemplo de uma PolyLine não fechada](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*Como corrigir inesperadaMenteGeometryInLayer*

Inspecione cada aviso **inesperado GeometryInLayer** e mova a geometria incompatível para uma camada compatível. Se não for compatível com nenhuma das outras camadas, deve ser removido.

### <a name="unsupportedfeaturerepresentation"></a>**Representação não suportada featurerepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*Descrição de Representação não suportada*

O aviso **não suportado FeatureRepresentation** ocorre quando o desenho contém um tipo de entidade não suportada.

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*Exemplo para A Representação de Características Não Suportadas*

A imagem abaixo mostra um tipo de entidade não suportada como um objeto de texto multi-linhas numa camada de etiqueta.
  
![Exemplo de um objeto de texto multi-linhas na camada de etiqueta](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*Como corrigir a Representação de Características não suportadas*

Certifique-se de que os seus ficheiros DWG contêm apenas os tipos de entidades suportadas. Os tipos suportados estão listados na [secção de requisitos dos ficheiros de desenho no artigo requisitos do pacote de desenho](drawing-requirements.md#drawing-package-requirements).

### <a name="automaticrepairperformed"></a>**reparação automáticaExecutada**

#### <a name="description-for-automaticrepairperformed"></a>*Descrição para reparação automática Executada*

O aviso **de reparação automática Ocorre** quando o serviço de conversão repara automaticamente a geometria inválida.

#### <a name="examples-for-automaticrepairperformed"></a>*Exemplos para reparação automáticaExecutada*

* A imagem seguinte mostra como o serviço de conversão reparou um polígono auto-interseccionado em geometria válida.

    ![Exemplo de um polígono auto-intersectado reparado](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* A imagem abaixo mostra como o serviço de Conversão estalou o primeiro e último vértice de um PolyLine não fechado para criar um PolyLine fechado, onde o primeiro e último vértice estavam a menos de 1 mm de distância.  

    ![Exemplo de um PolyLine estalado](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* A imagem abaixo mostra como, numa camada que suporta apenas polilines fechados, o serviço de Conversão reparou várias PolyLines não fechadas. A fim de evitar descartar as PolyLines não fechadas, o serviço combinou-os numa única PolyLine fechada.

    ![Exemplo de Polilines não fechados combinados numa única PolyLine fechada](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*Como corrigir o Reparo AutomáticoPerformed*

Para corrigir um aviso **automático RepairPerformed,** tome as seguintes ações:

1. Inspecione a geometria de cada aviso e o texto de aviso específico.
2. Determine se a reparação automática está correta.
3. Se a reparação estiver correta, continue. Caso contrário, vá ao ficheiro de design e resolva o aviso manualmente.

>[!TIP]
>Para suprimir um aviso no futuro, faça alterações no desenho original de modo a que o desenho original corresponda ao desenho reparado.

## <a name="manifest-warnings"></a>Alertas manifestos

### <a name="redundantattribution"></a>**atribuição redundante**

#### <a name="description-for-redundantattribution"></a>*Descrição para atribuição redundante*

O aviso **de atribuição redundante** ocorre quando o manifesto contém propriedades de objetos redundantes ou conflituosos.

#### <a name="examples-for-redundantattribution"></a>*Exemplos de atribuição redundante*

* O corte JSON abaixo contém dois ou mais `unitProperties` objetos com o mesmo `name` .

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* No corte JSON abaixo, dois ou mais `zoneProperties` objetos têm o mesmo `name` .

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*Como corrigir a atribuição redundante*

Para corrigir um aviso de*atribuição redundante,* remova propriedades de objetos redundantes ou conflituosos.

### <a name="manifestwarning"></a>**manifestoAviso**

#### <a name="description-for-manifestwarning"></a>*Descrição do manifestoAviso*

Um **manifestoO aviso** ocorre quando o manifesto contém objetos unitProperties ou zoneProperties que não são utilizados durante a conversão.

#### <a name="examples-for-manifestwarning"></a>*Exemplos para manifestoAviso*

* O manifesto contém um `unitProperties` objeto com um que não tem etiqueta correspondente numa `unitName` `unitLabel` camada.

* O manifesto contém um `zoneProperties` objeto com um que não tem etiqueta correspondente numa `zoneName` `zoneLabel` camada.

#### <a name="how-to-fix-manifestwarning"></a>*Como corrigir o manifestoAviso*

Para fixar um **manifestoAviso,** retire o objeto não utilizado `unitProperties` ou o objeto do manifesto ou adicione uma etiqueta unidade/zona ao desenho de modo a que o objeto de propriedades seja utilizado durante a `zoneProperties` conversão.

## <a name="wall-warnings"></a>Avisos de parede

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*Descrição para wallOutsideLevel*

O aviso **de paredeOutsideLevel** ocorre quando o desenho contém uma geometria da parede fora dos limites de um contorno de nível.

#### <a name="example-for-walloutsidelevel"></a>*Exemplo para wallOutsideLevel*

* A imagem abaixo mostra uma parede interior, em vermelho, fora do limite do nível amarelo.

    ![Exemplo de parede interior fora do limite de nível](./media/drawing-conversion-error-codes/wall-outside-level.png)

* A imagem seguinte mostra uma parede exterior, a vermelho, fora do limite do nível amarelo.

    ![Exemplo de parede exterior fora do limite de nível](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*Como corrigir wallOutsideLevel*

Para fixar um aviso **de paredeOutsideLevel,** expanda a geometria de nível para incluir todas as paredes. Ou modificar os limites da parede para caber dentro do limite de nível.

## <a name="unit-warnings"></a>Avisos unitários

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*Descrição da unidadeOutsideLevel*

Ocorre um aviso **unitOutsideLevel** quando o desenho contém geometria da unidade fora dos limites do contorno de nível.

#### <a name="example-for-unitoutsidelevel"></a>*Exemplo para unidadeOutsideLevel*

 Na imagem seguinte, a geometria da unidade, a vermelho, excede os limites do limite do nível amarelo.

 ![Exemplo de unidade que excede o limite de nível](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*Como corrigir a unidadeOutsideLevel*

Para fixar um aviso **unitOutsideLevel,** expanda o limite de nível para incluir todas as unidades. Ou modificar a geometria da unidade para caber dentro do limite de nível.

### <a name="partiallyoverlappingunit"></a>**Unidade parcialmente Sobreposta**

#### <a name="description-for-partiallyoverlappingunit"></a>*Descrição da Unidade de Sobreposição parcial*

Um aviso de Unidade **parcialmente sobreposta** ocorre quando o desenho contém uma geometria da unidade parcialmente sobreposta sobre outra geometria da unidade. O serviço de conversão descarta unidades sobrepostas.

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*Cenários de exemplo parcialmente SobrepostosUnit*

Na imagem seguinte, a unidade sobreposta é realçada a vermelho. `UNIT110`e `HALLWAY` são descartados.

![Exemplo de unidades sobrepostas](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*Como corrigir parcialmente a Unidade de Sobreposição*

Para corrigir um aviso de Unidade **parcialmente sobreposta,** redesenhar cada unidade parcialmente sobreposta para que não se sobreponha a quaisquer outras unidades.

## <a name="door-warnings"></a>Avisos de porta

### <a name="dooroutsidelevel"></a>**portaOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*Descrição para portaOutsideLevel*

Um aviso **de portaOutsideLevel** ocorre quando o desenho contém uma geometria da porta fora dos limites da geometria de nível.

#### <a name="example-for-dooroutsidelevel"></a>*Exemplo para portaOutsideLevel*

Na imagem seguinte, a geometria da porta, realçada a vermelho, sobrepõe-se ao limite do nível amarelo.

![Exemplo de uma porta sobreposta a um limite de nível](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*Como fixar a portaOutsideLevel*

Para fixar uma **porta OutsideLevel,** reaponte a geometria da porta de modo a que esteja dentro dos limites de nível.

## <a name="zone-warnings"></a>Avisos de zona

### <a name="zonewarning"></a>**zonaAviso**

#### <a name="description-for-zonewarning"></a>*Descrição da zonaAviso*

A **zonaO aviso** ocorre quando uma zona não contém uma etiqueta. O serviço de conversão descarta uma zona que não é etiqueta.l

#### <a name="example-for-zonewarning"></a>*Exemplo para zonaAviso*

A imagem que se segue mostra uma zona que não contém uma etiqueta.

![Exemplo de uma zona não contém um rótulo](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*Como corrigir a zonaAviso*

Para fixar uma **zonaAviso,** verifique se cada zona tem uma única etiqueta.

## <a name="label-warnings"></a>Avisos de Etiqueta

### <a name="labelwarning"></a>*rótuloAviso*

#### <a name="description-for-labelwarning"></a>*Descrição do rótuloAviso*

O **rótuloO aviso** ocorre quando o desenho contém uma função de etiquetas ambíguas ou contraditórias.

Um **rótuloO aviso** ocorre devido a uma ou mais das seguintes razões:

* Uma etiqueta de unidade não está em nenhuma unidade.
* Uma etiqueta de zona não está em nenhuma zona.
* Uma etiqueta de zona está dentro de duas ou mais zonas.

#### <a name="example-for-labelwarning"></a>*Exemplo para etiquetaAviso*

A imagem seguinte mostra um rótulo que dentro de duas zonas.

![Exemplo de um rótulo dentro de duas zonas ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*Como fixar rótuloAviso*

Para fixar uma **etiquetaAviso,** certifique-se de que:

* Todas as etiquetas de unidade estão dentro das unidades.
* Todos os rótulos da zona estão dentro de zonas.
* Todos os rótulos de zona estão em uma e única zona.

## <a name="drawing-package-errors"></a>Erros de pacote de desenho

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*Descrição do inválidoArchiveFormat*

Ocorre um erro **inválido do ArchiveFormat** quando o pacote de desenho se encontra num formato de arquivo inválido, como gZIP ou 7-Zip. Apenas o formato de arquivo ZIP é suportado.

Um erro **inválido do ArchiveFormat** também ocorrerá se o arquivo ZIP estiver vazio.

#### <a name="how-to-fix-invalidarchiveformat"></a>*Como corrigir o inválidoArchiveFormat*

Para corrigir um erro **inválido do ArchiveFormat,** verifique se:

* O nome do ficheiro do arquivo termina em _.zip_.
* O seu arquivo ZIP contém dados.
* Pode abrir o seu arquivo ZIP.

### <a name="invaliduserdata"></a>**inválidoUserData**

#### <a name="description-for-invaliduserdata"></a>*Descrição de InvalidUserData*

Ocorre um erro **inválidoUserData** quando o serviço de conversão não consegue ler um objeto de dados do utilizador a partir do armazenamento.

#### <a name="example-scenario-for-invaliduserdata"></a>*Cenário de exemplo para inválidoUserData*

Tentou carregar um pacote de desenho com um `udid` parâmetro incorreto.

#### <a name="how-to-fix-invaliduserdata"></a>*Como corrigir o inválidoUserData*

Para corrigir um erro **inválido do UserData,** verifique se:

* Forneceu um correto `udid` para o pacote enviado.
* O Azure Maps Creator foi ativado para a conta Azure Maps que usou para carregar o pacote Descarregamento.
* O pedido da API para o serviço de conversão contém a chave de subscrição da conta Azure Maps que utilizou para o upload do pacote Descarregamento.

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*Descrição do dwgError*

Um **dwgError** quando o pacote de desenho contém um problema com um ou mais ficheiros DWG no arquivo ZIP carregado.

O **dwgError** ocorre quando o pacote de desenho contém um ficheiro DWG que não pode ser aberto porque é inválido ou corrupto.

* Um ficheiro DWG não é um desenho válido do formato de ficheiro AutoCAD DWG.
* Um ficheiro DWG é corrupto.
* Um ficheiro DWG está listado no ficheiro _manifesto.json,_ mas está desaparecido do arquivo ZIP.

#### <a name="how-to-fix-dwgerror"></a>*Como corrigir o dwgError*

Para corrigir um **dwgError,** inspecione o ficheiro _manifesto.json_ confirme que:

* Todos os ficheiros DWG no seu arquivo ZIP são desenhos de formato AutoCAD DWG válidos, abram cada um em AutoCAD. Retire ou fixe todos os desenhos inválidos.
* A lista de ficheiros DWG no _manifesto.json_ corresponde aos ficheiros DWG no arquivo ZIP.

## <a name="manifest-errors"></a>Erros manifestos

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>Descrição do invalidJsonFormat

Um erro **inválido do JsonFormat** ocorre quando o ficheiro _manifesto.json_ não pode ser lido.

O _manifest.json_file não pode ser lido por causa de erros de formatação ou sintaxe da JSON. Para saber mais sobre como o formato JSON e a sintaxe, consulte o formato de troca de [dados do JavaScript (JSON)](https://tools.ietf.org/html/rfc7159)

#### <a name="how-to-fix-invalidjsonformat"></a>*Como corrigir o inválidoJsonFormat*

Para corrigir um erro **inválido jsonFormat,** utilize um linter JSON para detetar e resolver quaisquer erros JSON.

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*Descrição do campo desaparecido*

Um erro **de Campo Exigido em falta** ocorre quando o ficheiro _manifesto.json_ está em falta dos dados necessários.

#### <a name="how-to-fix-missingrequiredfield"></a>*Como corrigir o campo perdido*

Para corrigir um erro **de Campo Necessário em falta,** verifique se o manifesto contém todas as propriedades necessárias. Para obter uma lista completa do objeto manifesto exigido, consulte a [secção manifesto nos requisitos do pacote de desenho](drawing-requirements.md#manifest-file-requirements)  

### <a name="missingmanifest"></a>**missingManifestar**

#### <a name="description-for-missingmanifest"></a>*Descrição do manifesto desaparecido*

O erro **manifesto em falta** ocorre quando o ficheiro _manifesto.json_ está em falta no arquivo ZIP.

O erro **manifesto em falta** ocorre devido a uma ou mais das seguintes razões:

* O ficheiro _manifesto.json_ está mal escrito.
* O _manifesto.json_ desapareceu.
* O _manifesto.json_ não está dentro do diretório raiz do arquivo ZIP.

#### <a name="how-to-fix-missingmanifest"></a>*Como corrigir o Manifesto desaparecido*

Para corrigir um erro **manifesto em falta,** confirme que o arquivo tem um ficheiro chamado _manifesto.json_ ao nível raiz do arquivo ZIP.

### <a name="conflict"></a>**conflito**

#### <a name="description-for-conflict"></a>*Descrição do conflito*

O erro de **conflito** ocorre quando o ficheiro _manifesto.json_ contém informações contraditórias.

#### <a name="example-scenario-for-conflict"></a>*Exemplo de cenário de conflito*

O serviço de conversão devolverá um erro de **conflito** quando mais de um nível é definido com o mesmo nível de ordinal. O seguinte corte JSON mostra dois níveis definidos com o mesmo ordinal.

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*Como corrigir conflitos*

Para corrigir um erro de **conflito,** inspecione o seu _manifesto.json_ e remova qualquer informação conflituosa.

### <a name="invalidgeoreference"></a>**inválidaGeoreference**

#### <a name="description-for-invalidgeoreference"></a>*Descrição de inválidaGeoreference*

O erro **de referência inválido** ocorre quando um ficheiro _manifesto.json_ contém uma georeferência inválida.

O erro **de referência inválido** ocorre devido a uma ou mais das seguintes razões:

* O utilizador está a georeferenciar um valor de latitude ou longitude que está fora de alcance.
* O utilizador está a georeferenciar um valor de rotação fora de alcance.

#### <a name="example-scenario-for-invalidgeoreference"></a>*Exemplo de cenário para invalidação Georeference*

No corte JSON abaixo, a latitude está acima do limite superior.

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*Como corrigir inválidaGeoreference*

Para corrigir um erro **de referência inválido,** verifique se os valores georeferenciados estão dentro do alcance.

>[!IMPORTANT]
>Em GeoJSON, a ordem de coordenadas é longitude e latitude. Se não utilizar a ordem correta, poderá acidentalmente referir um valor de latitude ou longitude fora de alcance.

## <a name="wall-errors"></a>Erros de parede

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*Descrição do Erro de Parede*

O **Erro de parede** ocorre quando o desenho contém um erro enquanto tenta criar uma função de parede.

#### <a name="example-scenario-for-wallerror"></a>*Cenário de exemplo para wallError*

A imagem seguinte apresenta uma característica de parede que não se sobrepõe a nenhuma unidade.

![Exemplo de recurso de Parede que não se sobrepõe a nenhuma unidade](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*Como corrigir o Erro de Parede*

Para corrigir um erro de erro de erro de **parede,** redesenhar a parede de modo a sobrepor-se a pelo menos uma unidade. Ou criar uma nova unidade que sobreponha a parede.

## <a name="vertical-penetration-errors"></a>Erros de penetração vertical

### <a name="verticalpenetrationerror"></a>**verticalPenetraçãoError**

#### <a name="description-for-verticalpenetrationerror"></a>*Descrição do VerticalPenetrationError*

O **VerticalPenetrationError** ocorre quando o desenho contém uma função de penetração vertical ambígua.

O Erro vertical de **Penetração** ocorre devido a uma ou mais das seguintes razões:

* O desenho contém uma área de penetração vertical sem áreas de penetração vertical sobrepostas em quaisquer níveis acima ou abaixo dele.
* O pacote de desenho contém um nível com duas ou mais características de penetração vertical que se sobrepõem a uma única característica de penetração vertical em outro nível diretamente acima ou abaixo dele.

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*Cenário de exemplo para verticalPenetraçãoError*

A imagem abaixo mostra uma área de penetração vertical sem sobreposição de áreas de penetração vertical em níveis acima ou abaixo dela.

![Exemplo de uma penetração vertical 1](./media/drawing-conversion-error-codes/vrt-2.png)

A imagem seguinte mostra uma área de penetração vertical que se sobrepõe a mais de uma área de penetração vertical num nível adjacente.

![Exemplo de uma penetração vertical 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>Como corrigir o Erro de Penetração vertical

Para corrigir um erro vertical de **Penetração Error,** leia sobre como usar uma função de penetração vertical no artigo de requisitos do [pacote de desenho.](drawing-requirements.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como utilizar o visualizador de erro de desenho do Azure Maps](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [Criador para mapeamento interior](creator-indoor-maps.md)