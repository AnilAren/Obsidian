
HF_MODELS_PATH = APP_ROOT.parent / "hf-models"  
 
PROMPT_INJECTION_MODEL_PATH = (  
    HF_MODELS_PATH / "ProtectAI" / "deberta-v3-base-prompt-injection-v2"  
)  
 
TOXICITY_MODEL_PATH = HF_MODELS_PATH / "unitary" / "unbiased-toxic-roberta"  
PII_MODEL_PATH = HF_MODELS_PATH / "Isotonic" / "deberta-v3-base_finetuned_ai4privacy_v2"  
scanners = {} 
 
 