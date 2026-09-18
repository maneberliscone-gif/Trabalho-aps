# Trabalho-aps
Processamento e a segmentação de imagens médicas, com foco específico na extração e separação da região dos pulmões (campos pulmonares) a partir de radiografias de tórax (raio-X) 


  Faculdade Metropolitanas Unidas
Artefato 1 - Requisitos e Pesquisa de Ferramentas
Autores: Eduardo Bonamelli, Berliscone Mané e Renato Fernandes
São Paulo, 2026
________________________________________
1. Introdução
Este artefato apresenta os requisitos iniciais para o desenvolvimento de um projeto de processamento e segmentação de imagens médicas, com foco na utilização de radiografias de tórax (imagens de raio X) para realizar a segmentação da região pulmonar. A segmentação de imagens médicas consiste em identificar e separar regiões específicas presentes em uma imagem, sendo que neste projeto a região de interesse será formada pelos pulmões, possibilitando posteriormente análises e processamentos específicos.
As imagens utilizadas serão provenientes de conjuntos de dados públicos, especificamente da base Montgomery, que disponibiliza imagens anonimizadas com máscaras de referência dos pulmões.
1.1 Domínio / Aplicação
O projeto será desenvolvido no domínio de processamento e análise de imagens médicas, utilizando radiografias de tórax. O objetivo principal é realizar a segmentação dos campos pulmonares, separando as regiões correspondentes aos pulmões do restante da radiografia. Essa segmentação servirá como etapa de pré-processamento para análises posteriores de estruturas e possíveis alterações pulmonares.
1.2 Origem e Licença
As imagens utilizadas serão provenientes do Montgomery County Chest X-ray Set, disponibilizado pela National Library of Medicine (NLM), instituição vinculada aos National Institutes of Health (NIH), dos Estados Unidos. O conjunto contém 138 radiografias de tórax, sendo 80 casos classificados como normais e 58 casos com manifestações de tuberculose. As imagens foram identificadas e o conjunto também possui anotações manuais das regiões pulmonares realizadas sob supervisão de radiologista.
Os arquivos serão utilizados de acordo com as condições de uso e disponibilização estabelecidas pela fonte oficial, sem atribuição de licença CC0 ou MIT sem que essa licença esteja explicitamente indicada pela fonte.
1.3 Formato do Arquivo
O formato utilizado pelo dataset é PNG (Portable Network Graphics), que utiliza compressão sem perdas, sendo adequado para preservar os valores dos pixels durante o armazenamento e processamento das imagens. Além das radiografias, o dataset disponibiliza máscaras binárias correspondentes aos pulmões direito e esquerdo, também em formato PNG, que poderão ser utilizadas como referência para avaliar os resultados dos algoritmos de segmentação.
1.4 Resolução Espacial
As imagens originais do conjunto Montgomery apresentam matriz de 4020 × 4892 pixels ou 4892 × 4020 pixels, dependendo da imagem. O espaçamento dos pixels informado na documentação é de 0,0875 mm nos sentidos vertical e horizontal. Para reduzir o custo computacional e padronizar a entrada dos algoritmos, será utilizada uma resolução de 1024 × 1024 pixels durante a etapa de processamento, preservando as características relevantes da imagem tanto quanto possível.
1.5 Profundidade de Intensidade
As imagens originais do dataset possuem 12 bits de profundidade, correspondendo a até 4096 níveis de intensidade de cinza, permitindo representar uma faixa maior de valores de intensidade do que uma imagem convencional de 8 bits, que possui 256 níveis de cinza. Para algumas etapas do processamento, poderá ser realizada uma conversão controlada para 8 bits, caso isso seja necessário para a implementação dos algoritmos selecionados, mantendo os arquivos originais preservados para referência.
1.6 Orçamento de Armazenamento
Para estimar o armazenamento, considera-se uma imagem padronizada em 1024 × 1024 pixels com profundidade de 12 bits por pixel:
1024 × 1024 = 1.048.576 pixels 1.048.576 × 12 bits = 12.582.912 bits 12.582.912 ÷ 8 = 1.572.864 bytes
Portanto, uma imagem de 1024 × 1024 pixels com 12 bits por pixel necessita de aproximadamente 1,5 MiB de armazenamento bruto. Para um conjunto de 50 imagens: 50 × 1.572.864 = 78.643.200 bytes, ocupando aproximadamente 75 MiB de armazenamento bruto, desconsiderando a compressão utilizada pelo formato PNG. O tamanho real dos arquivos PNG pode ser menor, pois o formato utiliza compressão sem perdas.
________________________________________
2. Pesquisa de Algoritmos de Segmentação
Para o desenvolvimento do pipeline de segmentação dos campos pulmonares, foram pesquisadas diferentes abordagens de processamento e segmentação de imagens, incluindo Limiarização de Otsu, K-Means, operações morfológicas e métodos baseados em redes neurais como a U-Net.
2.1 Limiarização de Otsu
O método de Otsu é uma técnica automática de limiarização baseada no histograma da imagem. O algoritmo procura determinar um valor de limiar que maximize a separação estatística entre classes de intensidade, podendo ser utilizado para separar determinadas regiões da imagem de seu fundo.
Vantagens: Baixo custo computacional, não necessita de treinamento, não necessita de GPU, possui implementação relativamente simples e permite analisar e reproduzir de maneira clara as etapas utilizadas.
Limitações: O método pode apresentar dificuldades quando as regiões de interesse possuem intensidades semelhantes às demais estruturas da imagem ou quando o histograma não apresenta uma separação adequada entre as classes. Por esse motivo, será utilizado em conjunto com técnicas de processamento morfológico para tentar melhorar o resultado da segmentação.
2.2 Agrupamento por K-Means
O K-Means é um algoritmo de agrupamento que organiza os dados em diferentes grupos chamados de clusters. No processamento de imagens, os pixels podem ser agrupados de acordo com suas características de intensidade, permitindo separar diferentes regiões da imagem.
Vantagens: Implementação relativamente simples, pode separar mais de duas classes de intensidade, não exige treinamento supervisionado e permite comparar diferentes agrupamentos de regiões da imagem.
Limitações: É necessário definir previamente o número de grupos, o resultado pode variar de acordo com a inicialização dos centróides e o algoritmo utiliza principalmente características numéricas dos pixels, podendo não considerar diretamente a estrutura espacial da imagem.
2.3 Operações Morfológicas
As operações morfológicas são técnicas utilizadas para modificar e tratar imagens binárias ou em escala de cinza. Neste projeto serão consideradas principalmente as operações de abertura e fechamento morfológico. A abertura pode ser utilizada para remover pequenos elementos e ruídos, enquanto o fechamento pode auxiliar no preenchimento de pequenas lacunas e na conexão de regiões próximas. Essas operações poderão ser aplicadas após a segmentação inicial realizada pelo método de Otsu, com o objetivo de melhorar a forma das regiões pulmonares obtidas.
2.4 U-Net
A U-Net é uma arquitetura de rede neural desenvolvida especificamente para tarefas de segmentação de imagens biomédicas. A arquitetura utiliza um caminho de contração para extração de características e um caminho de expansão para recuperar informações espaciais e produzir a segmentação da imagem.
Vantagens: Grande capacidade de aprendizado de características complexas, adequada para segmentação de imagens biomédicas e pode produzir máscaras detalhadas quando treinada adequadamente.
Limitações: Necessita de dados anotados para treinamento, exige maior capacidade computacional, possui implementação mais complexa do que os métodos clássicos e o treinamento e avaliação demandam maior tempo de desenvolvimento. Por esses motivos, a U-Net será considerada como uma possibilidade de comparação futura, mas não será o método principal da primeira implementação do projeto.
________________________________________
3. Levantamento do Dataset
3.1 Dataset Selecionado
O dataset selecionado para o projeto é o Montgomery County Chest X-ray Set, disponibilizado pela National Library of Medicine. O conjunto contém 138 radiografias de tórax, sendo 80 casos classificados como normais e 58 casos com manifestações compatíveis com tuberculose. As imagens estão disponíveis em formato PNG e possuem profundidade original de 12 bits, com dimensões de aproximadamente 4020 × 4892 pixels ou 4892 × 4020 pixels. O dataset também disponibiliza máscaras manuais correspondentes aos pulmões direito e esquerdo, que serão utilizadas como referência para comparar os resultados obtidos pelos algoritmos de segmentação.
3.2 Quantidade de Imagens
Para atender ao requisito estabelecido no projeto, foi selecionado um conjunto inicial de 50 radiografias de tórax pertencentes ao dataset Montgomery County Chest X-ray Set. As 50 imagens serão armazenadas em uma pasta específica do projeto, mantendo seus nomes originais para facilitar a identificação e a rastreabilidade dos arquivos. Para cada imagem selecionada, também serão mantidas as respectivas máscaras de referência dos pulmões, quando disponíveis. A seleção das imagens será realizada diretamente a partir dos arquivos disponibilizados pela National Library of Medicine.
3.3 Organização dos Arquivos
O conjunto de dados será organizado em pastas para facilitar o desenvolvimento do projeto. As radiografias selecionadas serão armazenadas em uma pasta denominada "Imagens", enquanto as máscaras correspondentes serão armazenadas em uma pasta denominada "Máscaras". Também será mantida uma pasta de "Documentação", contendo as informações sobre a origem do dataset, suas características e condições de uso. A utilização das imagens originais juntamente com suas respectivas máscaras permitirá realizar posteriormente a comparação entre a segmentação produzida pelos algoritmos Otsu e K-Means e as máscaras de referência fornecidas pelo dataset.
3.4 Fonte do Dataset
As imagens e as informações relacionadas ao dataset serão obtidas a partir da documentação e dos arquivos disponibilizados pela National Library of Medicine. O conjunto Montgomery County Chest X-ray Set possui quantidade suficiente de imagens para atender ao requisito mínimo de 50 radiografias estabelecido para o projeto.
________________________________________
4. Escolha Justificada dos Métodos
O grupo optou por implementar e comparar dois métodos principais de segmentação: Otsu, associado a operações morfológicas, e K-Means. A escolha dos métodos clássicos está relacionada ao escopo da disciplina de Processamento de Imagem e Sinais e à possibilidade de implementar os algoritmos sem a necessidade de treinamento de redes neurais.
Inicialmente, o método de Otsu será aplicado para realizar a limiarização da imagem. Em seguida, operações morfológicas de abertura e fechamento poderão ser utilizadas para reduzir ruídos e melhorar a continuidade das regiões segmentadas. O K-Means será utilizado como segundo método para possibilitar uma comparação entre duas abordagens clássicas de segmentação. Os resultados obtidos poderão ser comparados com as máscaras de referência dos pulmões disponibilizadas pelo dataset Montgomery, permitindo avaliar a qualidade da segmentação realizada pelos algoritmos. A U-Net será apresentada como uma abordagem moderna de segmentação, mas não será utilizada como método principal nesta etapa devido à maior complexidade computacional e à necessidade de treinamento.
________________________________________
5. Conclusão
Neste Artefato 1 foram definidos os requisitos iniciais do projeto PulmoSan, voltado ao processamento e à segmentação de imagens de radiografia de tórax. Foi selecionado o Montgomery County Chest X-ray Set, disponibilizado pela National Library of Medicine, por possuir radiografias de tórax, informações sobre os casos e máscaras manuais de referência dos pulmões. O dataset possui 138 imagens, permitindo selecionar as 50 imagens mínimas exigidas para o projeto.
A ficha técnica definiu o formato PNG, a profundidade original de 12 bits e a resolução original das imagens. Para o processamento computacional, será utilizada uma resolução padronizada de 1024 × 1024 pixels. Foram pesquisados e comparados os métodos Otsu, K-Means, operações morfológicas e U-Net. Como estratégia inicial, o grupo utilizará Otsu associado a operações morfológicas e K-Means, permitindo comparar diferentes abordagens clássicas de segmentação. A utilização das máscaras de referência disponíveis no dataset permitirá avaliar posteriormente os resultados obtidos e verificar a qualidade da segmentação dos campos pulmonares.
________________________________________
Referências
NATIONAL LIBRARY OF MEDICINE. Montgomery County Chest X-ray Database — ReadMe. National Institutes of Health. Disponível em: documentação oficial do dataset Montgomery. Acesso em: 09 set. 2026.
OTSU, N. A Threshold Selection Method from Gray-Level Histograms. IEEE Transactions on Systems, Man, and Cybernetics, v. 9, n. 1, p. 62–66, 1979.
RONNEBERGER, O.; FISCHER, P.; BROX, T. U-Net: Convolutional Networks for Biomedical Image Segmentation. Medical Image Computing and Computer-Assisted Intervention (MICCAI), 2015.

