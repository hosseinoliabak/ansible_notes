# YAML
* YAML is a sort of plaintext syntax format
* Extension is `.yml`
```
name: Hossein
last_name: Oliabak
certifications:
  - "CCNP R&S"
  - ITILF
websites:
  linkedin: https://www.linkedin.com/in/hosseinoliabak/
  github: https://github.com/hosseinoliabak
```
* Here `certifications` is a list (Order matters)
* Here `websites` is a dictionary (Order doesn't matter)
* All ansible playbook are written in YAML

**In YAML**
```
Devices:
  - name: R1
    owner: Denis
    created: 12162017
    location: lab_8
```

**Compare to JSON**
```
{
  "Devices": [
    {
      "name": "R1"
      "owner": "Denis",
      "created": 12162017,
      "location": "lab_8",
    }
  ]
}
```

All YAML files (regardless of their association with Ansible or not) can
optionally begin with `---` and end with `...` . This is part of the
YAML format and indicates the start and end of a document.