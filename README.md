with open(r"C:\Users\m7967\Desktop\parsing\data.txt") as file:
    file_conrents = file.read()
    import re
    import pandas as pd
    rx_dict = {
        'school': re.compile(r'School = (?P<school>.*)\n'),
        'grade': re.compile(r'Grade = (?P<grade>\d+)\n'),
        'name_score': re.compile(r'(?P<name_score>Name|Score)'),
    }
    def _parse_line(line):
        for key, rx in rx_dict.items():
            match = rx.search(line)
            if match:
                return key, match
        return None, None
    def parse_file(filepath):
        data = []
        with open(filepath, 'r') as file_object:
            line = file_object.readline()
            while line:
                key, match = _parse_line(line)
                if key == 'school':
                    school = match.group('school')
                if key == 'grade':
                    grade = match.group('grade')
                    grade = int(grade)
                if key == 'name_score':
                    value_type = match.group('name_score')
                    line = file_object.readline()
                    while line.strip():
                        number, value = line.strip().split(',')
                        value = value.strip()
                        row = {
                            'School': school,
                            'Grade': grade,
                            'Student number': number,
                            value_type: value
                        }
                        data.append(row)
                        line = file_object.readline()
                line = file_object.readline()
            data = pd.DataFrame(data)
            data.set_index(['School', 'Grade', 'Student number'], inplace=True)
            data = data.groupby(level=data.index.names).first()
            data = data.apply(pd.to_numeric, errors='ignore')
        return data
    if __name__ == '__main__':
        filepath = r"C:\Users\m7967\Desktop\parsing\data.txt"
        data = parse_file(r"C:\Users\m7967\Desktop\parsing\data.txt")
        print(data)
