class Student:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}

    def mid_grade(self):
        self.all_grades = []
        for value in self.grades.values():
            self.all_grades.extend(value)
        return round(sum(self.all_grades) / len(self.all_grades), 2)

    def rate_lectors(self, lecturer, course, grade):
        if isinstance(lecturer,
                      Lecturer) and course in lecturer.courses_attached and course in self.courses_in_progress:
            if course in lecturer.lec_grades:
                lecturer.lec_grades[course] += [grade]
            else:
                lecturer.lec_grades[course] = [grade]
        else:
            return 'Ошибка'

    def __lt__(self, other):
        if not isinstance(other, Student):
            print('Не наш студент')
            return
        else:
            return self.mid_grade() < other.mid_grade()

    def __str__(self):
        res = f'Имя: {self.name}\nФамилия: {self.surname}\nСредняя оценка за домашние задания: {self.mid_grade()}\nКурсы в процессе изучения: {", ".join(self.courses_in_progress)}\nЗавершенные курсы: {", ".join(self.finished_courses)}'
        return res


class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []


class Lecturer(Mentor):
    def __init__(self, name, surname, courses_attached):
        super().__init__(name, surname)
        self.courses_attached = courses_attached
        self.lec_grades = {}

    def mid_lec_grade(self):
        self.all_lec_grades = []
        for value in self.lec_grades.values():
            self.all_lec_grades.extend(value)
        return round(sum(self.all_lec_grades) / len(self.all_lec_grades), 2)

    def __lt__(self, other):
        if not isinstance(other, Lecturer):
            print('Не наш преподователь')
            return
        else:
            return self.mid_lec_grade() < other.mid_lec_grade()

    def __str__(self):
        res = f'Имя: {self.name}\nФамилия: {self.surname}\nСредняя оценка за лекции: {self.mid_lec_grade()}'
        return res


class Reviewer(Mentor):
    def __init__(self, name, surname, courses_attached):
        super().__init__(name, surname)
        self.courses_attached = courses_attached

    def __str__(self):
        res = f'Имя: {self.name}\nФамилия: {self.surname}'
        return res

    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course] += [grade]
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'


some_student = Student('Алексей', 'Евсеев')

student_list = [some_student]


def mid_grade_course(student_list, course):
    all_student_avg_grades = []
    for stu in student_list:
        if course not in stu.courses_in_progress:
            return 'Студент курс не проходит'
        else:
            all_student_avg_grades.extend(stu.grades[course])
            s = sum(all_student_avg_grades)
            l = len(all_student_avg_grades)
    return s / l


some_student.courses_in_progress += ['Python', 'Введение в программирование']
some_student.finished_courses += ['GIT']

some_lecturer = Lecturer('Олег', 'Булыгин', ['Python', 'Введение в программирование', 'GIT'])

some_student.rate_lectors(some_lecturer, 'Введение в программирование', 9)
some_student.rate_lectors(some_lecturer, 'Python', 10)
some_student.rate_lectors(some_lecturer, 'GIT', 7)

some_reviewer = Reviewer('Алена', 'Батицкая', ['Python', 'Введение в программирование', 'GIT'])

print(some_reviewer.__str__())
print(some_lecturer.__lt__(some_lecturer))
print(some_lecturer.__str__())

some_reviewer.rate_hw(some_student, 'Python', 6)
some_reviewer.rate_hw(some_student, 'Python', 9)
some_reviewer.rate_hw(some_student, 'GIT', 7)

print(some_student.__str__())
