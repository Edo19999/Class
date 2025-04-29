class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}

    def rate_lecturer(self, lecturer, course, grade):
        if isinstance(lecturer, Lecturer) and course in self.courses_in_progress and course in lecturer.courses_attached:
            lecturer.grades.setdefault(course, []).append(grade)

    def average_grade(self):
        all_grades = [g for grades in self.grades.values() for g in grades]
        return round(sum(all_grades) / len(all_grades), 1) if all_grades else 0

    def __str__(self):
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}\n"
                f"Средняя оценка за домашние задания: {self.average_grade()}\n"
                f"Курсы в процессе изучения: {', '.join(self.courses_in_progress)}\n"
                f"Завершенные курсы: {', '.join(self.finished_courses)}")

    def __lt__(self, other):
        return self.average_grade() < other.average_grade()


class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []


class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.grades = {}

    def average_grade(self):
        all_grades = [g for grades in self.grades.values() for g in grades]
        return round(sum(all_grades) / len(all_grades), 1) if all_grades else 0

    def __str__(self):
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}\n"
                f"Средняя оценка за лекции: {self.average_grade()}")

    def __lt__(self, other):
        return self.average_grade() < other.average_grade()


class Reviewer(Mentor):
    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            student.grades.setdefault(course, []).append(grade)

    def __str__(self):
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}")


student1 = Student('Иван', 'Иванов', 'м')
student1.courses_in_progress += ['Python', 'Git']
student1.finished_courses += ['Введение в программирование']

student2 = Student('Анна', 'Смирнова', 'ж')
student2.courses_in_progress += ['Python']
student2.finished_courses += ['Git']

lecturer1 = Lecturer('Сергей', 'Петров')
lecturer1.courses_attached += ['Python']

lecturer2 = Lecturer('Мария', 'Иванова')
lecturer2.courses_attached += ['Git', 'Python']

reviewer1 = Reviewer('Елена', 'Васильева')
reviewer1.courses_attached += ['Python', 'Git']

reviewer2 = Reviewer('Дмитрий', 'Соколов')
reviewer2.courses_attached += ['Python']

reviewer1.rate_hw(student1, 'Python', 9)
reviewer1.rate_hw(student1, 'Git', 10)
reviewer2.rate_hw(student2, 'Python', 8)
reviewer2.rate_hw(student1, 'Python', 10)

student1.rate_lecturer(lecturer1, 'Python', 9)
student1.rate_lecturer(lecturer2, 'Python', 8)
student2.rate_lecturer(lecturer1, 'Python', 10)

print(student1)
print()
print(student2)
print()
print(lecturer1)
print()
print(lecturer2)
print()
print(reviewer1)
print()
print(reviewer2)
print()

print("Сравнение студентов:", student1 > student2)
print("Сравнение лекторов:", lecturer1 > lecturer2)

def average_grade_students(students, course):
    all_grades = []
    for s in students:
        all_grades.extend(s.grades.get(course, []))
    return round(sum(all_grades) / len(all_grades), 1) if all_grades else 0

def average_grade_lecturers(lecturers, course):
    all_grades = []
    for l in lecturers:
        all_grades.extend(l.grades.get(course, []))
    return round(sum(all_grades) / len(all_grades), 1) if all_grades else 0

print("\nСредняя оценка студентов по Python:", average_grade_students([student1, student2], 'Python'))
print("Средняя оценка лекторов по Python:", average_grade_lecturers([lecturer1, lecturer2], 'Python'))
