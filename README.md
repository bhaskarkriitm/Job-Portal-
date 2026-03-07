# Job-Portal- App Code Python 

from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///job_portal.db'
db = SQLAlchemy(app)

# Database Model for Jobs
class Job(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    company = db.Column(db.String(100), nullable=False)
    description = db.Column(db.Text, nullable=False)

# Route to post a new job
@app.route('/post-job', methods=['POST'])
def post_job():
    data = request.json
    new_job = Job(title=data['title'], company=data['company'], description=data['description'])
    db.session.add(new_job)
    db.session.commit()
    return jsonify({"message": "Job posted successfully!"}), 201

# Route to view all jobs
@app.route('/jobs', methods=['GET'])
def get_jobs():
    jobs = Job.query.all()
    output = []
    for job in jobs:
        output.append({"id": job.id, "title": job.title, "company": job.company})
    return jsonify(output)

if __name__ == '__main__':
    with app.app_context():
        db.create_all()
    app.run(debug=True)
