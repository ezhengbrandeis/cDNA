# cDNA
from django.shortcuts import render

BASE_PAIRING = {
    'A': 'T',
    'C': 'G',
    'G': 'C',
    'T': 'A',
    'U': 'A'
}

map_s  = {"UUU":"F", "UUC":"F", "UUA":"L", "UUG":"L",
    "UCU":"S", "UCC":"s", "UCA":"S", "UCG":"S",
    "UAU":"Y", "UAC":"Y", "UAA":"STOP", "UAG":"STOP",
    "UGU":"C", "UGC":"C", "UGA":"STOP", "UGG":"W",
    "CUU":"L", "CUC":"L", "CUA":"L", "CUG":"L",
    "CCU":"P", "CCC":"P", "CCA":"P", "CCG":"P",
    "CAU":"H", "CAC":"H", "CAA":"Q", "CAG":"Q",
    "CGU":"R", "CGC":"R", "CGA":"R", "CGG":"R",
    "AUU":"I", "AUC":"I", "AUA":"I", "AUG":"M",
    "ACU":"T", "ACC":"T", "ACA":"T", "ACG":"T",
    "AAU":"N", "AAC":"N", "AAA":"K", "AAG":"K",
    "AGU":"S", "AGC":"S", "AGA":"R", "AGG":"R",
    "GUU":"V", "GUC":"V", "GUA":"V", "GUG":"V",
    "GCU":"A", "GCC":"A", "GCA":"A", "GCG":"A",
    "GAU":"D", "GAC":"D", "GAA":"E", "GAG":"E",
    "GGU":"G", "GGC":"G", "GGA":"G", "GGG":"G",}

def home(request):
    context = {}

    # Check whether this request includes a query string to translate.
    query_string = request.GET.get('query', None)
    if query_string:
        query_string = query_string.upper()
        reverse_complement = ''
        error_class = 'white'
        codons = ""
        new_qs = ""
        for base in query_string:
            comp = BASE_PAIRING.get(base, None)
            if base == 'T':
               base = 'U'
            new_qs += base
            if not comp:
                error_class = 'red'
                reverse_complement = ''
                break
            reverse_complement += comp
        query_string = new_qs
        if error_class == 'white':
           for i, j, k in zip(query_string[0::3], query_string[1::3], query_string[2::3]) :
             seq = i + j + k
             codon = map_s.get(seq, None)
             if codon:
                codons += codon
                continue
             break
        context['query_string'] = query_string
        context['reverse_complement'] = reverse_complement
        context['error_class'] = error_class
        context['codons'] = codons
    return render(request, 'home.html', context)
