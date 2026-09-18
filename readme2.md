"""
FACULDADE METROPOLITANAS UNIDAS
Artefato 1 - Requisitos e Pesquisa de Ferramentas

Autores: Eduardo Bonamelli, Berliscone Mané e Renato Fernandes
Local: São Paulo
Ano: 2026

Descrição:
Este artefato apresenta os requisitos iniciais para o desenvolvimento de um projeto 
de processamento e segmentação de imagens médicas, com foco na utilização de 
radiografias de tórax para realizar a segmentação da região pulmonar.
"""

# ============================================================================
# 1. INTRODUÇÃO
# ============================================================================

INTRODUCAO = """
Este artefato apresenta os requisitos iniciais para o desenvolvimento de um projeto de 
processamento e segmentação de imagens médicas. O projeto terá como foco a utilização 
de radiografias de tórax, também conhecidas como imagens de raio X, com o objetivo de 
realizar a segmentação da região pulmonar.

A segmentação de imagens médicas consiste em identificar e separar regiões específicas 
presentes em uma imagem. Neste projeto, a região de interesse será formada pelos pulmões, 
possibilitando posteriormente a realização de análises e processamentos específicos nesta região.

Para o desenvolvimento do projeto, serão utilizadas imagens de radiografia de tórax 
provenientes de conjuntos de dados públicos. O conjunto escolhido possui imagens 
anonimizadas e, no caso da base Montgomery, também disponibiliza máscaras de referência 
dos pulmões.
"""

# ============================================================================
# 2. FICHA TÉCNICA DA IMAGEM MÉDICA
# ============================================================================

FICHA_TECNICA = {
    "dominio_aplicacao": {
        "titulo": "Domínio / Aplicação",
        "conteudo": """
        O projeto será desenvolvido no domínio de processamento e análise de imagens médicas, 
        utilizando radiografias de tórax (Chest X-ray).

        O objetivo principal é realizar a segmentação dos campos pulmonares, separando as 
        regiões correspondentes aos pulmões do restante da radiografia. Essa segmentação 
        poderá servir como etapa de pré-processamento para análises posteriores de estruturas 
        e possíveis alterações pulmonares.
        """
    },

    "origem_licenca": {
        "titulo": "Origem e Licença",
        "conteudo": """
        As imagens utilizadas no projeto serão provenientes do Montgomery County Chest X-ray 
        Set, disponibilizado pela National Library of Medicine (NLM), instituição vinculada 
        aos National Institutes of Health (NIH), dos Estados Unidos.

        O conjunto contém 138 radiografias de tórax, sendo 80 casos classificados como 
        normais e 58 casos com manifestações de tuberculose. As imagens foram identificadas, 
        e o conjunto também possui anotações manuais das regiões pulmonares realizadas sob 
        supervisão de radiologista.

        Para fins de conformidade, os arquivos serão utilizados de acordo com as condições 
        de uso e disponibilização estabelecidas pela fonte oficial. Não será atribuída ao 
        dataset uma licença CC0 ou MIT sem que essa licença esteja explicitamente indicada 
        pela fonte oficial.
        """
    },

    "formato_arquivo": {
        "titulo": "Formato do Arquivo",
        "formato": "PNG (Portable Network Graphics)",
        "conteudo": """
        O formato PNG utiliza compressão sem perdas, sendo adequado para preservar os 
        valores dos pixels durante o armazenamento e processamento das imagens.

        Além das radiografias, o dataset disponibiliza máscaras binárias correspondentes 
        aos pulmões direito e esquerdo, também em formato PNG. Essas máscaras poderão ser 
        utilizadas como referência para avaliar os resultados dos algoritmos de segmentação.
        """
    },

    "resolucao_espacial": {
        "titulo": "Resolução Espacial",
        "resolucao_original": "4020 × 4892 pixels ou 4892 × 4020 pixels",
        "espacamento_pixels": "0,0875 mm (vertical e horizontal)",
        "resolucao_processamento": "1024 × 1024 pixels",
        "conteudo": """
        As imagens originais do conjunto Montgomery apresentam matriz de 4020 × 4892 pixels 
        ou 4892 × 4020 pixels, dependendo da imagem. O espaçamento dos pixels informado na 
        documentação é de 0,0875 mm nos sentidos vertical e horizontal.

        Para reduzir o custo computacional e padronizar a entrada dos algoritmos, será 
        utilizada uma resolução de 1024 × 1024 pixels durante a etapa de processamento. 
        Essa redução será realizada de maneira controlada, preservando as características 
        relevantes da imagem tanto quanto possível.
        """
    },

    "profundidade_intensidade": {
        "titulo": "Profundidade de Intensidade",
        "profundidade_original": "12 bits",
        "niveis_intensidade": 4096,
        "profundidade_convencional": "8 bits",
        "niveis_convencional": 256,
        "conteudo": """
        As imagens originais do dataset possuem 12 bits de profundidade, correspondendo a 
        até 4096 níveis de intensidade de cinza. Essa característica permite representar 
        uma faixa maior de valores de intensidade do que uma imagem convencional de 8 bits, 
        que possui 256 níveis de cinza.

        Para algumas etapas do processamento, poderá ser realizada uma conversão controlada 
        para 8 bits, caso isso seja necessário para a implementação dos algoritmos 
        selecionados. A conversão será realizada somente quando necessária, mantendo os 
        arquivos originais preservados para referência.
        """
    }
}

# ============================================================================
# 3. ORÇAMENTO DE ARMAZENAMENTO
# ============================================================================

class OrcamentoArmazenamento:
    """Cálculo do orçamento de armazenamento para as imagens"""

    def __init__(self):
        self.largura = 1024
        self.altura = 1024
        self.profundidade_bits = 12
        self.quantidade_imagens = 50

    def calcular_pixels(self):
        """Calcula o número total de pixels"""
        return self.largura * self.altura

    def calcular_bits(self):
        """Calcula o número total de bits"""
        return self.calcular_pixels() * self.profundidade_bits

    def calcular_bytes(self):
        """Converte bits para bytes"""
        return self.calcular_bits() / 8

    def calcular_mib(self):
        """Converte bytes para MiB"""
        return self.calcular_bytes() / (1024 * 1024)

    def calcular_conjunto(self):
        """Calcula o armazenamento para o conjunto de 50 imagens"""
        bytes_total = self.calcular_bytes() * self.quantidade_imagens
        mib_total = bytes_total / (1024 * 1024)
        return bytes_total, mib_total

    def exibir_calculo(self):
        """Exibe o cálculo detalhado"""
        print("=" * 70)
        print("ORÇAMENTO DE ARMAZENAMENTO")
        print("=" * 70)
        print(f"\nImagem Individual (1024 × 1024 pixels, 12 bits):")
        print(f"  Pixels: {self.calcular_pixels():,}")
        print(f"  Bits: {self.calcular_bits():,}")
        print(f"  Bytes: {self.calcular_bytes():,.0f}")
        print(f"  MiB: {self.calcular_mib():.2f}")

        bytes_conjunto, mib_conjunto = self.calcular_conjunto()
        print(f"\nConjunto de {self.quantidade_imagens} Imagens:")
        print(f"  Bytes Totais: {bytes_conjunto:,.0f}")
        print(f"  MiB Totais: {mib_conjunto:.2f}")
        print(f"\nNota: O tamanho real dos arquivos PNG pode ser menor devido à")
        print(f"compressão sem perdas utilizada pelo formato.")
        print("=" * 70)

# ============================================================================
# 4. PESQUISA DE ALGORITMOS DE SEGMENTAÇÃO
# ============================================================================

ALGORITMOS_SEGMENTACAO = {
    "otsu": {
        "nome": "Limiarização de Otsu",
        "descricao": """
        O método de Otsu é uma técnica automática de limiarização baseada no histograma 
        da imagem. O algoritmo procura determinar um valor de limiar que maximize a 
        separação estatística entre classes de intensidade. Em uma aplicação de segmentação, 
        esse limiar pode ser utilizado para separar determinadas regiões da imagem de seu fundo.
        """,
        "vantagens": [
            "Baixo custo computacional",
            "Não necessita de treinamento",
            "Não necessita de GPU",
            "Possui implementação relativamente simples",
            "Permite analisar e reproduzir de maneira clara as etapas utilizadas"
        ],
        "limitacoes": [
            "Pode apresentar dificuldades quando as regiões de interesse possuem intensidades semelhantes às demais estruturas da imagem",
            "Pode falhar quando o histograma não apresenta uma separação adequada entre as classes",
            "Será utilizado em conjunto com técnicas de processamento morfológico para melhorar o resultado"
        ]
    },

    "kmeans": {
        "nome": "Agrupamento por K-Means",
        "descricao": """
        O K-Means é um algoritmo de agrupamento que organiza os dados em diferentes grupos, 
        chamados de clusters. No processamento de imagens, os pixels podem ser agrupados de 
        acordo com suas características de intensidade. Para a segmentação, pode-se utilizar, 
        por exemplo, dois ou mais grupos para representar diferentes regiões da imagem.
        """,
        "vantagens": [
            "Implementação relativamente simples",
            "Pode separar mais de duas classes de intensidade",
            "Não exige treinamento supervisionado",
            "Permite comparar diferentes agrupamentos de regiões da imagem"
        ],
        "limitacoes": [
            "É necessário definir previamente o número de grupos",
            "O resultado pode variar de acordo com a inicialização dos centróides",
            "Utiliza principalmente características numéricas dos pixels",
            "Pode não considerar diretamente a estrutura espacial da imagem"
        ]
    },

    "operacoes_morfologicas": {
        "nome": "Operações Morfológicas",
        "descricao": """
        As operações morfológicas são técnicas utilizadas para modificar e tratar imagens 
        binárias ou em escala de cinza. Neste projeto serão consideradas principalmente as 
        operações de abertura e fechamento morfológico.
        """,
        "operacoes": {
            "abertura": "Pode ser utilizada para remover pequenos elementos e ruídos",
            "fechamento": "Pode auxiliar no preenchimento de pequenas lacunas e na conexão de regiões próximas"
        },
        "aplicacao": """
        Essas operações poderão ser aplicadas após a segmentação inicial realizada pelo 
        método de Otsu, com o objetivo de melhorar a forma das regiões pulmonares obtidas.
        """
    },

    "unet": {
        "nome": "U-Net",
        "descricao": """
        A U-Net é uma arquitetura de rede neural desenvolvida especificamente para tarefas 
        de segmentação de imagens biomédicas. A arquitetura utiliza um caminho de contração 
        para extração de características e um caminho de expansão para recuperar informações 
        espaciais e produzir a segmentação da imagem.
        """,
        "vantagens": [
            "Grande capacidade de aprendizado de características complexas",
            "Adequada para segmentação de imagens biomédicas",
            "Pode produzir máscaras detalhadas quando treinada adequadamente"
        ],
        "limitacoes": [
            "Necessita de dados anotados para treinamento",
            "Exige maior capacidade computacional",
            "Possui implementação mais complexa do que os métodos clássicos",
            "O treinamento e a avaliação demandam maior tempo de desenvolvimento"
        ],
        "nota": """
        A U-Net será considerada como uma possibilidade de comparação futura, mas não será 
        o método principal da primeira implementação do projeto.
        """
    }
}

# ============================================================================
# 5. LEVANTAMENTO DO DATASET
# ============================================================================

DATASET_MONTGOMERY = {
    "nome": "Montgomery County Chest X-ray Set",
    "instituicao": "National Library of Medicine (NLM)",
    "vinculacao": "National Institutes of Health (NIH), Estados Unidos",
    "total_imagens": 138,
    "casos_normais": 80,
    "casos_tuberculose": 58,
    "formato": "PNG",
    "profundidade": "12 bits",
    "dimensoes": "4020 × 4892 ou 4892 × 4020 pixels",
    "mascara_disponivel": True,
    "mascara_descricao": "Máscaras manuais dos pulmões direito e esquerdo",
    "imagens_selecionadas": 50,
    "anotacoes": "Anotações manuais das regiões pulmonares realizadas sob supervisão de radiologista"
}

ORGANIZACAO_ARQUIVOS = {
    "estrutura": {
        "Dataset": {
            "Imagens": "50 radiografias selecionadas",
            "Máscaras": "Máscaras correspondentes dos pulmões",
            "Documentação": "Informações sobre origem, dataset e condições de uso"
        }
    },
    "descricao": """
    O conjunto de dados será organizado em pastas para facilitar o desenvolvimento do projeto. 
    As radiografias selecionadas serão armazenadas em uma pasta denominada "Imagens", enquanto 
    as máscaras correspondentes serão armazenadas em uma pasta denominada "Máscaras". Também 
    será mantida uma pasta de "Documentação", contendo as informações sobre a origem do dataset, 
    suas características e condições de uso.

    A utilização das imagens originais juntamente com suas respectivas máscaras permitirá 
    realizar posteriormente a comparação entre a segmentação produzida pelos algoritmos Otsu 
    e K-Means e as máscaras de referência fornecidas pelo dataset.
    """
}

# ============================================================================
# 6. ESCOLHA JUSTIFICADA DOS MÉTODOS
# ============================================================================

ESCOLHA_METODOS = """
O grupo optou por implementar e comparar dois métodos principais de segmentação: Otsu, 
associado a operações morfológicas, e K-Means.

A escolha dos métodos clássicos está relacionada ao escopo da disciplina de Processamento 
de Imagem e Sinais e à possibilidade de implementar os algoritmos sem a necessidade de 
treinamento de redes neurais.

Inicialmente, o método de Otsu será aplicado para realizar a limiarização da imagem. Em 
seguida, operações morfológicas de abertura e fechamento poderão ser utilizadas para reduzir 
ruídos e melhorar a continuidade das regiões segmentadas.

O K-Means será utilizado como segundo método para possibilitar uma comparação entre duas 
abordagens clássicas de segmentação.

Os resultados obtidos poderão ser comparados com as máscaras de referência dos pulmões 
disponibilizadas pelo dataset Montgomery, permitindo avaliar a qualidade da segmentação 
realizada pelos algoritmos.

A U-Net será apresentada como uma abordagem moderna de segmentação, mas não será utilizada 
como método principal nesta etapa devido à maior complexidade computacional e à necessidade 
de treinamento.
"""

# ============================================================================
# 7. CONCLUSÃO
# ============================================================================

CONCLUSAO = """
Neste Artefato 1 foram definidos os requisitos iniciais do projeto PulmoSan, voltado ao 
processamento e à segmentação de imagens de radiografia de tórax.

Foi selecionado o Montgomery County Chest X-ray Set, disponibilizado pela National Library 
of Medicine, por possuir radiografias de tórax, informações sobre os casos e máscaras manuais 
de referência dos pulmões. O dataset possui 138 imagens, permitindo selecionar as 50 imagens 
mínimas exigidas para o projeto.

A ficha técnica definiu o formato PNG, a profundidade original de 12 bits e a resolução 
original das imagens. Para o processamento computacional, será utilizada uma resolução 
padronizada de 1024 × 1024 pixels.

Foram pesquisados e comparados os métodos Otsu, K-Means, operações morfológicas e U-Net. 
Como estratégia inicial, o grupo utilizará Otsu associado a operações morfológicas e K-Means, 
permitindo comparar diferentes abordagens clássicas de segmentação.

A utilização das máscaras de referência disponíveis no dataset permitirá avaliar posteriormente 
os resultados obtidos e verificar a qualidade da segmentação dos campos pulmonares.
"""

# ============================================================================
# 8. REFERÊNCIAS
# ============================================================================

REFERENCIAS = [
    {
        "autor": "NATIONAL LIBRARY OF MEDICINE",
        "titulo": "Montgomery County Chest X-ray Database — ReadMe",
        "instituicao": "National Institutes of Health",
        "data_acesso": "09 set. 2026"
    },
    {
        "autor": "OTSU, N.",
        "titulo": "A Threshold Selection Method from Gray-Level Histograms",
        "revista": "IEEE Transactions on Systems, Man, and Cybernetics",
        "volume": "9",
        "numero": "1",
        "paginas": "62–66",
        "ano": "1979"
    },
    {
        "autor": "RONNEBERGER, O.; FISCHER, P.; BROX, T.",
        "titulo": "U-Net: Convolutional Networks for Biomedical Image Segmentation",
        "conferencia": "Medical Image Computing and Computer-Assisted Intervention (MICCAI)",
        "ano": "2015"
    }
]

# ============================================================================
# 9. FUNÇÃO PRINCIPAL PARA EXIBIÇÃO
# ============================================================================

def exibir_relatorio():
    """Exibe o relatório completo do Artefato 1"""
    print("\n" + "=" * 70)
    print("FACULDADE METROPOLITANAS UNIDAS")
    print("ARTEFATO 1 - REQUISITOS E PESQUISA DE FERRAMENTAS")
    print("=" * 70)
    print("\nAutores: Eduardo Bonamelli, Berliscone Mané e Renato Fernandes")
    print("Local: São Paulo")
    print("Ano: 2026")
    print("\n" + "=" * 70)
    print("INTRODUÇÃO")
    print("=" * 70)
    print(INTRODUCAO)

    # Exibir orçamento de armazenamento
    orcamento = OrcamentoArmazenamento()
    orcamento.exibir_calculo()

    # Exibir dataset
    print("\n" + "=" * 70)
    print("DATASET MONTGOMERY")
    print("=" * 70)
    for chave, valor in DATASET_MONTGOMERY.items():
        print(f"{chave.replace('_', ' ').title()}: {valor}")

    # Exibir conclusão
    print("\n" + "=" * 70)
    print("CONCLUSÃO")
    print("=" * 70)
    print(CONCLUSAO)

    print("\n" + "=" * 70)
    print("FIM DO ARTEFATO 1")
    print("=" * 70 + "\n")

# ============================================================================
# EXECUÇÃO
# ============================================================================

if __name__ == "__main__":
    exibir_relatorio()